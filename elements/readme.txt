Quads:
Перепиши предоставленный исходный код Delphi (Object Pascal, VCL + OpenGL) в современный HTML5-приложение, состоящее из одного файла index.html без внешних зависимостей.

Требования:

Использовать только:
HTML5
CSS3
JavaScript (ES6+)
WebGL или Three.js (если используется Three.js, подключать через CDN).
Полностью сохранить логику оригинальной программы:
Отображение 8000 объектов.
Плавная интерполяция между различными фигурами.
Автоматическое вращение сцены.
Анимация переходов между состояниями.
Управление с клавиатуры.

Реализовать структуры данных, эквивалентные:

TQuad
TQuads
Сохранить все режимы анимации:
0 — случайное облако точек.
1 — сфера.
2 — прямоугольная решетка.
3 — объемный куб.
4 — плоскость.
5 — увеличенная плоскость.
6 — вертикальная плоскость XY.
7 — вертикальная плоскость XZ.
8 — окружность.
9 — фигура Лиссажу.
10 — концентрические сферы.
11 — дополнительная сферическая форма.

Для каждой частицы хранить:

{
    x: Number,
    y: Number,
    z: Number,
    size: Number
}
Реализовать метод перемещения аналогично Delphi-коду:
вычисление расстояния до целевой точки;
движение со скоростью speed;
фиксация позиции при достижении цели.
Управление клавиатурой:
клавиши 0–9 переключают режимы;
клавиша ` переключает режим 10;
клавиша + увеличивает скорость;
клавиша - уменьшает скорость.
Визуализация:
черный фон;
белые частицы;
перспективная камера;
вращение сцены вокруг нескольких осей;
плавная анимация через requestAnimationFrame.

Не разбивать код на несколько файлов.
Сгенерируй полностью готовый файл:

<!DOCTYPE html>
<html>
...
</html>
Код должен запускаться сразу после открытия файла в браузере без дополнительной настройки.
Добавь комментарии, объясняющие соответствие между Delphi-кодом и JavaScript-реализацией.

Выведи только готовый HTML-код без дополнительных пояснений.



unit Unit1; interface uses Windows, Forms, OpenGL, Classes, ExtCtrls, Messages, Controls, StdCtrls; type TMainForm = class(TForm) Timer1: TTimer; Timer2: TTimer; procedure FormCreate(Sender: TObject); procedure FormDestroy(Sender: TObject); procedure FormPaint(Sender: TObject); procedure FormKeyPress(Sender: TObject; var Key: Char); procedure FormResize(Sender: TObject); procedure Timer1Timer(Sender: TObject); procedure FormClose(Sender: TObject; var Action: TCloseAction); procedure FormCloseQuery(Sender: TObject; var CanClose: Boolean); private b:boolean; RC : HGLRC; Angle : GLfloat; procedure WMEraseBkgnd(var Message: TWMEraseBkgnd); message WM_ERASEBKGND; end; TQuad=record x,y,z,a:glfloat; end; const maxanim=11; CObj=8000; type TQuads=object NewQuads:array [0..maxanim,1..CObj] of TQuad; Quads:array [1..CObj] of TQuad; anim:byte; Speed:GLfloat; constructor Create; procedure Paint; procedure Move; destructor Destroy; end; var MainForm: TMainForm; FixQuads:TQuads; implementation uses Dialogs; procedure kub(x,y,z,a:GLfloat); begin glBegin(GL_TRIANGLE_FAN); glColor3f(1,1,1); glVertex3f(x,y,z+a); glVertex3f(x+a,y,z); glVertex3f(x,y+a,z); glVertex3f(x-a,y,z); glVertex3f(x,y-a,z); glVertex3f(x+a,y,z); glEnd; glBegin(GL_TRIANGLE_FAN); glColor3f(1,1,1); glVertex3f(x,y,z-a); glVertex3f(x+a,y,z); glVertex3f(x,y+a,z); glVertex3f(x-a,y,z); glVertex3f(x,y-a,z); glVertex3f(x+a,y,z); glEnd; end; {$R *.DFM} constructor TQuads.Create; var i,j,k:longint; ii:longint; r,g,b:extended; begin randomize; for i:=1 to CObj do begin NewQuads[0,i].x:=5-random*10; NewQuads[0,i].y:=5-random*10; NewQuads[0,i].z:=5-random*10; NewQuads[0,i].a:=0.05; end; for i:=1 to CObj do begin g:=2*pi*random; b:=2*pi*random; r:=5*(1-2*random); NewQuads[1,i].x:=r*cos(g)*sin(b); NewQuads[1,i].y:=r*sin(g)*sin(b); NewQuads[1,i].z:=r*cos(b); NewQuads[1,i].a:=0.05; end; ii:=0; for i:=-20 to 19 do for j:=-20 to 19 do for k:=0 to 4 do begin ii:=ii+1; NewQuads[2,ii].x:=i/10; NewQuads[2,ii].y:=j/10; NewQuads[2,ii].z:=k/10; NewQuads[2,ii].a:=0.05; end; ii:=0; for i:=-10 to 9 do for j:=-10 to 9 do for k:=-10 to 9 do begin ii:=ii+1; NewQuads[3,ii].x:=i/10; NewQuads[3,ii].y:=j/10; NewQuads[3,ii].z:=k/10; NewQuads[3,ii].a:=0.05; end; ii:=0; for i:=-20 to 19 do for j:=0 to 4 do for k:=-20 to 19 do begin ii:=ii+1; NewQuads[4,ii].x:=i/10; NewQuads[4,ii].y:=j/10; NewQuads[4,ii].z:=k/10; NewQuads[4,ii].a:=0.05; end; ii:=0; for i:=-20 to 19 do for j:=0 to 4 do for k:=-20 to 19 do begin ii:=ii+1; NewQuads[5,ii].x:=i/5; NewQuads[5,ii].y:=j/5; NewQuads[5,ii].z:=k/5; NewQuads[5,ii].a:=0.05; end; ii:=0; k:=0; for i:=-40 to 39 do for j:=-50 to 49 do begin ii:=ii+1; NewQuads[6,ii].x:=i/10; NewQuads[6,ii].y:=j/10; NewQuads[6,ii].z:=k/10; NewQuads[6,ii].a:=0.05; end; ii:=0; j:=0; for i:=-40 to 39 do for k:=-50 to 49 do begin ii:=ii+1; NewQuads[7,ii].x:=i/10; NewQuads[7,ii].y:=j/10; NewQuads[7,ii].z:=k/10; NewQuads[7,ii].a:=0.05; end; k:=0; for ii:=1 to CObj do begin NewQuads[8,ii].x:=5*cos(ii*pi/4000); NewQuads[8,ii].y:=5*sin(ii*pi/4000); NewQuads[8,ii].z:=k; NewQuads[8,ii].a:=0.05; end; k:=0; for ii:=1 to CObj do begin NewQuads[9,ii].x:=5*cos(ii*pi/4000); NewQuads[9,ii].y:=5*sin(ii*2*pi/4000); NewQuads[9,ii].z:=k; NewQuads[9,ii].a:=0.05; end; ii:=0; for i:=1 to 40 do for j:=1 to 40 do for k:=1 to 5 do begin ii:=ii+1; NewQuads[10,ii].x:=(k/3)*cos(i*pi/20)*sin(j*pi/20); NewQuads[10,ii].y:=(k/3)*sin(i*pi/20)*sin(j*pi/20); NewQuads[10,ii].z:=(k/3)*cos(j*pi/20); NewQuads[10,ii].a:=0.05; end; ii:=0; for i:=1 to 40 do for j:=1 to 40 do for k:=1 to 5 do begin ii:=ii+1; NewQuads[11,ii].x:=(k/3)*cos(i*pi/20)*sin(j*pi/20); NewQuads[11,ii].y:=(k/3)*sin(i*pi/20)*sin(j*pi/20); NewQuads[11,ii].z:=(k/3)*cos(j*pi/20); NewQuads[11,ii].a:=0.05; end; for i:=1 to CObj do begin Quads[i].x:=0; Quads[i].y:=0; Quads[i].z:=0; Quads[i].a:=0.05; end; anim:=random(maxanim); Speed:=(1/30); end; procedure TQuads.Paint; var i:longword; begin for i:=1 to CObj do kub(Quads[i].x,Quads[i].y,Quads[i].z,Quads[i].a); end; destructor TQuads.Destroy; begin end; procedure TQuads.Move; var i:longint; absvector:GLfloat; anim0:boolean; x,y,z:GLfloat; begin anim0:=true; for i:=1 to CObj do begin x:=Quads[i].x-NewQuads[anim,i].x; y:=Quads[i].y-NewQuads[anim,i].y; z:=Quads[i].z-NewQuads[anim,i].z; absvector:=sqrt(sqr(x)+sqr(y)+sqr(z)); if (absvector>0.05) and (absvector>speed) then begin anim0:=false; Quads[i].x:=Quads[i].x-(x/absvector)*speed; Quads[i].y:=Quads[i].y-(y/absvector)*speed; Quads[i].z:=Quads[i].z-(z/absvector)*speed; end else begin Quads[i].x:=NewQuads[anim,i].x; Quads[i].y:=NewQuads[anim,i].y; Quads[i].z:=NewQuads[anim,i].z; end; end; if anim0 then if (random+speed>0.95) then anim:=random(maxanim); end; procedure TMainForm.FormCreate(Sender: TObject); begin // initialize OpenGL and create a context for rendering b:=true; FixQuads.Create; RC:=CreateRenderingContext(Canvas.Handle,[opDoubleBuffered],32,0); end; procedure TMainForm.FormDestroy(Sender: TObject); begin // destroy the rendering context DestroyRenderingContext(RC); end; procedure lightinitr; const Ambient : Array[0..3] of GLfloat = (1, 0.9, 0.9, 0.7); dif: Array[0..3] of GLfloat = (0, 0, 0, 1); em: Array[0..3] of GLfloat = (0, 0, 0, 0.1); spec: Array[0..3] of GLfloat = (0 ,0, 1, 1); specl: Array[0..3] of GLfloat = (1, 0, 1, 1); pos:array[0..3]of glFloat=(10,10,10,1); dir:array[0..2]of glFloat=(-2.2,-1,-1); var c_o:single; c:integer; s_e:glFloat; begin s_e:=5; c_o:=90; c:=1; glEnable(gl_color_material); glLightModelFV(gl_light_model_local_viewer,@c); glLightFV(gl_light1,gl_spot_cutoff,@c_o); glLightFV(gl_light1,gl_position,@pos); glLightFV(gl_light1,gl_spot_direction,@dir); glLightFV(gl_light1,gl_ambient,@ambient); glLightFV(gl_light1,gl_specular,@specl); glLightFV(gl_light1,gl_spot_exponent,@s_e); glMaterialfv(GL_FRONT_and_back, GL_AMBIENT, @ambient); glMaterialfv(GL_FRONT_and_back, GL_DIFFUSE, @dif); glMaterialfv(GL_FRONT_and_back, GL_SPECULAR, @spec); glMaterialfv(GL_FRONT_and_back, GL_emission, @em); end; procedure TMainForm.FormPaint(Sender: TObject); begin // draw somthing useful ActivateRenderingContext(Canvas.Handle,RC); // make context drawable glClearColor(0,0,0,1); // background color of the context} glClear(GL_COLOR_BUFFER_BIT or GL_DEPTH_BUFFER_BIT); // clear background and depth buffer glMatrixMode(GL_MODELVIEW); // activate the transformation matrix glLoadIdentity; // set it to initial state gluLookAt(1,1,15,0,0,0,0,0,1); // set up a viewer position and view point } if b then begin { gluLookAt(0,0,-6,-2,-2,10,-10,-10,0); // set up a viewer position and view point } //glTranslatef(0,0,0); // do rotation around axis (x:0;y:1;z:0) { glRotatef(30,1,0,0); // do another rotation for better view (accumulates to first rot.) { glScalef(1,sin(Angle*pi/90),1); // simulate bumping} glEnable(GL_DEPTH_TEST); // enable depth testing lightinitr; glEnable(GL_LIGHT1); glEnable(GL_LIGHTING); b:=false; end; glRotatef(Angle,0.5,1,1); FixQuads.Paint; SwapBuffers(Canvas.Handle); // copy back buffer to front DeactivateRenderingContext; // release control of context end; procedure TMainForm.FormKeyPress(Sender: TObject; var Key: Char); begin if Key = #27 then Application.Terminate; if ('0' <= Key) and (Key <= '9') then fixquads.anim:=ord(key)-48; if (Key = '') or (Key = '~') then fixquads.anim:=10; if (Key = '+') or (Key = '=') then fixquads.speed:=fixquads.speed*1.1; if Key = '-' then fixquads.speed:=fixquads.speed*0.9; if fixquads.speed>1 then fixquads.speed:=1; if fixquads.speed<(1/300) then fixquads.speed:=1/300; end; procedure TMainForm.FormResize(Sender: TObject); begin // handle form resizing (viewport must be adjusted) wglMakeCurrent(Canvas.handle,RC); // another way to make context drawable glViewport(0,0,Width,Height); // specify a viewport (has not necessarily to be the entire window) glMatrixMode(GL_PROJECTION); // activate projection matrix glLoadIdentity; // set initial state gluPerspective(35,Width/Height,1,100); // specify perspective params (see OpenGL.hlp) wglMakeCurrent(0,0); // another way to release control of context Refresh; // cause redraw end; procedure TMainForm.Timer1Timer(Sender: TObject); begin // do some animation if mainform.Visible then FixQuads.Move; Angle:=Angle+1; if Angle >= 360 then Angle:=Angle-360; Repaint; end; procedure TMainForm.WMEraseBkgnd(var Message: TWMEraseBkgnd); begin // avoid clearing the background (causes flickering and speed penalty) Message.Result:=1; end; procedure TMainForm.FormClose(Sender: TObject; var Action: TCloseAction); begin Application.Terminate; end; procedure TMainForm.FormCloseQuery(Sender: TObject; var CanClose: Boolean); begin Application.Terminate; end; end.

Roza:
Создай полностью автономное приложение на HTML5 + JavaScript + WebGL (без внешних библиотек, кроме стандартного WebGL API), которое является функциональным аналогом программы на Delphi/OpenGL.

Требования:

1. Отобразить 3D-сцену WebGL на весь экран браузера.
2. Реализовать рекурсивную генерацию фрактальных деревьев:

   * Функция Branches() должна строить ствол и ветви.
   * Ветвление должно происходить рекурсивно с случайными углами и длинами.
   * При достижении малой длины вместо новых ветвей генерировать листья.
3. Реализовать функцию Rose():

   * Создавать объемный цветок из множества лепестков.
   * Цвет лепестков случайно выбирается между белыми, красными и синими оттенками.
   * Использовать несколько слоев лепестков для создания бутона.
4. Создать собственный генератор псевдослучайных чисел по аналогии с объектом Trnd:

   * Хранить массив из 20000 случайных значений.
   * Использовать циклическое чтение массива.
   * Поддерживать reset() и resetTop().
5. Реализовать два типа деревьев:

   * Branches (основной вариант).
   * OldBranches (старое дерево с другой геометрией).
6. На сцене размещать поле из множества случайно сгенерированных растений:

   * Для каждого растения генерировать дерево.
   * На вершине дерева размещать розу.
   * Иногда вместо розы создавать дерево типа OldBranches.
7. Реализовать освещение:

   * Ambient Light.
   * Diffuse Light.
   * Spot Light.
   * Материалы объектов.
8. Камера должна работать аналогично Delphi-коду:

   * Режим обзора всего поля.
   * Режим просмотра одного растения.
   * Переключение режимов клавишей X.
9. Реализовать анимацию:

   * Автоматическое вращение сцены.
   * Обновление угла через requestAnimationFrame.
   * Периодическая регенерация поля растений.
10. Управление клавиатурой:

    * ESC — остановить анимацию.
    * A / D — ручное вращение.
    * S — включить/выключить автоанимацию.
    * X — переключить режим камеры.
    * SPACE — пересоздать случайное поле и сбросить угол.
    * * / - — изменение положения камеры.
11. Использовать матрицы преобразований:

    * translate
    * rotate
    * perspective projection
12. Код должен быть хорошо структурирован:

    * класс RandomPool
    * класс PlantGenerator
    * класс RoseGenerator
    * класс Camera
    * класс Renderer
13. Весь код должен находиться в одном HTML-файле.
14. Не использовать Three.js. Использовать только чистый WebGL.
15. Добавить подробные комментарии к каждому значимому блоку кода.
16. Результат должен визуально напоминать поле фантастических деревьев с розами, аналогичное оригинальной Delphi/OpenGL программе.

Сгенерируй полный готовый HTML-файл целиком, включая canvas, JavaScript, шейдеры WebGL, обработчики клавиатуры и цикл рендеринга.



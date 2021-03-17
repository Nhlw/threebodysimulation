# threebodysimulation
%三维的三体模型模拟

%%
for k = 1:10
clear

%设定参数
sim.duration = 1000000;
sim.step = 0.01;
pos1 = 1e7*rand(1,3);
pos2 = 1e7*rand(1,3);
pos3 = 1e7*rand(1,3);
pos = [pos1;pos2;pos3];

v1 = 1e3*rand(1,3);
v2 = 1e3*rand(1,3);
v3 = 1e3*rand(1,3);


a1 = [0 0 0];
a2 = [0 0 0];
a3 = [0 0 0];
%a_x a_y

m1 = 1e30*rand();
m2 = 1e30*rand();
m3 = 1e30*rand();


G = 6.67*10^-11;

%密度
%{
p1 = 100000;
p2 = 100000;
p3 = 100000;
part.p = [p1;p2;p3];

%半径
r1 = (0.75*m1/(p1*pi))^(1/3);
r2 = (0.75*m2/(p2*pi))^(1/3);
r3 = (0.75*m3/(p3*pi))^(1/3);
%}

x = 1.08e-18;
r1 = m1*x;
r2 = m2*x;
r3 = m3*x;
h = figure;

%
%计算
t_tot = sim.duration;
t_step = sim.step;

dt_v = t_step;
dt_f = 0.5*t_step;
step = 1;

t_start = t_step;
count = 0;

    an1 = animatedline;
    an1.Color = 'r';
    an2 = animatedline;
    an2.Color = 'g';
    an3 = animatedline;
    an3.Color = 'b';

    
hold on;
grid on;
axis equal;
p1 = plot3(pos1(1),pos1(2),pos1(3),'r:.','markersize',30);
p2 = plot3(pos2(1),pos2(2),pos2(3),'g:.','markersize',30);
p3 = plot3(pos3(1),pos3(2),pos3(3),'b:.','markersize',30);   

set(gcf,'unit','normalized','position',[0.2,0.2,0.6,0.6]);
uiwait(msgbox('这是一个简单的模拟三体运动的程序'));
for t=t_start:t_step:t_tot
   
     %距离的计算
    r12 = normest(pos2-pos1);
    r13 = normest(pos3-pos1);
    r23 = normest(pos3-pos2);
    
   
    n12 = (pos2-pos1)./r12;
    n13 = (pos3-pos1)./r13;
    n23 = (pos3-pos2)./r23;
    
    F12 = G*m1*m2/(r12^2);
    F13 = G*m1*m3/(r13^2);
    F23 = G*m2*m3/(r23^2);
   
    
    a1 = (F12*n12+F13*n13)/m1;
    a2 = (F12*(-n12)+F23*n23)/m2;
    a3 = (F23*(-n13)+F23*(-n23))/m3;
   
    
    v1 = a1*t_step+v1;
    v2 = a2*t_step+v2;
    v3 = a3*t_step+v3;
    
    pos1 = t_step*v1+pos1;
    pos2 = t_step*v2+pos2;    
    pos3 = t_step*v3+pos3; 
    %}
    %{
    old_vel = part.vel;
    part.vel = part.vel+dt_f*part.a;
    
    part.pos = part.pos+old_vel*dt_v+0.5*part.a*dt_v^2;
    
    %}
    %力的单位位矢计算
  %{
    F12_len = G*m1*m2/(r12^2);
    F23_len = G*m1*m2/(r23^2);
    F13_len = G*m1*m2/(r13^2);
    
    F12_dir = (pos2-pos1)/normest(pos2-pos1);
    F23_dir = (pos3-pos2)/normest(pos3-pos2);
    F13_dir = (pos3-pos1)/normest(pos3-pos1);
     
   %a的计算
    a1 = ((F12_len)*F12_dir+(F13_len)*F13_dir)/m1;
    a2 = ((-F12_len)*F12_dir+(F23_len)*F23_dir)/m2;
    a3 = ((-F23_len)*F23_dir+(-F13_len)*F13_dir)/m3;
    
    v1 = a1*t_step+v1;
    v2 = a2*t_step+v2;
    v3 = a3*t_step+v3;
    
    pos1 = t_step*v1+pos1;
    pos2 = t_step*v2+pos2;    
    pos3 = t_step*v3+pos3; 
    %}
    step = step+1;

    %动画创建
    set(p1,'Xdata',pos1(1),'Ydata',pos1(2),'Zdata',pos1(3));
    set(p2,'Xdata',pos2(1),'Ydata',pos2(2),'Zdata',pos2(3));
    set(p3,'Xdata',pos3(1),'Ydata',pos3(2),'Zdata',pos3(3));
    addpoints(an1,pos1(1),pos1(2),pos1(3));
    addpoints(an2,pos2(1),pos2(2),pos2(3));
    addpoints(an3,pos3(1),pos3(2),pos3(3));
    drawnow
    
  %if r12<1.26*r2 || r13<1.26*r1 || r23<1.26*r2
      if r12<100000 || r13<100000 || r23<100000 
       uiwait(msgbox('两星相撞或有恒星距离超过洛希极限'));
       close all
       break
      else if r12>0.5e8 || r13>0.5e8 || r23>0.5e8
              uiwait(msgbox('有恒星逃逸三体系统'));
              close all
              break
          end
   end
end
end

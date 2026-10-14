
clc;
clear;
close all;

%% MICROWAVE ENGINEERING - EXPERIMENT 1
% TEM, TE and TM Mode Analysis
% All Four Objectives in ONE Figure

%% CONSTANTS AND WAVEGUIDE DIMENSIONS

c = 3e8;

% Rectangular waveguide dimensions
a = 0.02;       % Width in metres
b = 0.01;       % Height in metres

% Operating frequency
f0 = 10e9;      % 10 GHz
k0 = 2*pi*f0/c;

%% SPATIAL GRID

x = linspace(0,a,60);
y = linspace(0,b,60);
[X,Y] = meshgrid(x,y);

%% OBJECTIVE 1: ELECTROMAGNETIC MODE COMPARISON

% Normalized illustrative electric-field patterns
E_TE10 = sin(pi*X/a);
E_TE20 = sin(2*pi*X/a);
E_TM11 = sin(pi*X/a).*sin(pi*Y/b);

% TEM field illustration (two-conductor structure)
r = linspace(1,10,60);
theta = linspace(0,2*pi,100);
[R,THETA] = meshgrid(r,theta);

X_TEM = R.*cos(THETA);
Y_TEM = R.*sin(THETA);
E_TEM = 1./R;

%% OBJECTIVE 2: ELECTRIC AND MAGNETIC FIELD DISTRIBUTIONS

% Illustrative normalized field patterns
E_field = E_TE10;

H_field = abs(cos(pi*X/a));

%% OBJECTIVE 3: CUTOFF FREQUENCIES

fc_TEM = 0;
fc_TE10 = c/(2*a);
fc_TE20 = c/a;
fc_TM11 = (c/2)*sqrt((1/a)^2 + (1/b)^2);

%% PROPAGATION CONSTANTS

beta_TEM = k0;

% TE10 propagation constant
if f0 > fc_TE10
    beta_TE10 = k0*sqrt(1-(fc_TE10/f0)^2);
else
    beta_TE10 = NaN;
end

% TE20 propagation constant
if f0 > fc_TE20
    beta_TE20 = k0*sqrt(1-(fc_TE20/f0)^2);
else
    beta_TE20 = NaN;
end

% TM11 propagation constant
if f0 > fc_TM11
    beta_TM11 = k0*sqrt(1-(fc_TM11/f0)^2);
else
    beta_TM11 = NaN;
end

%% FREQUENCY SWEEP

freq = linspace(1e9,30e9,500);
k = 2*pi*freq/c;

betaTEM = k;

betaTE10 = NaN(size(freq));
idx = freq > fc_TE10;
betaTE10(idx) = sqrt(k(idx).^2-(2*pi*fc_TE10/c)^2);

betaTE20 = NaN(size(freq));
idx = freq > fc_TE20;
betaTE20(idx) = sqrt(k(idx).^2-(2*pi*fc_TE20/c)^2);

betaTM11 = NaN(size(freq));
idx = freq > fc_TM11;
betaTM11(idx) = sqrt(k(idx).^2-(2*pi*fc_TM11/c)^2);

%% OBJECTIVE 4: PROPAGATION STATUS AT OPERATING FREQUENCY

% 1 = propagating, 0 = below cutoff
status = [1, f0 > fc_TE10, f0 > fc_TE20, f0 > fc_TM11];

%% COMBINE ALL GRAPHS IN ONE FIGURE

figure('Name','Microwave Engineering - All Objectives', ...
       'Color','w','Position',[50 50 1500 900]);

%% GRAPH 1: TE10 MODE

subplot(3,3,1);
surf(X*1000,Y*1000,E_TE10);
shading interp;
xlabel('X (mm)');
ylabel('Y (mm)');
zlabel('Normalized E-field');
title('Objective 1: TE_{10} Mode');
colorbar;
view(45,30);
grid on;

%% GRAPH 2: TE20 MODE

subplot(3,3,2);
surf(X*1000,Y*1000,E_TE20);
shading interp;
xlabel('X (mm)');
ylabel('Y (mm)');
zlabel('Normalized E-field');
title('Objective 1: TE_{20} Mode');
colorbar;
view(45,30);
grid on;

%% GRAPH 3: TM11 MODE

subplot(3,3,3);
surf(X*1000,Y*1000,E_TM11);
shading interp;
xlabel('X (mm)');
ylabel('Y (mm)');
zlabel('Normalized E-field');
title('Objective 1: TM_{11} Mode');
colorbar;
view(45,30);
grid on;

%% GRAPH 4: TEM MODE

subplot(3,3,4);
surf(X_TEM,Y_TEM,E_TEM);
shading interp;
xlabel('X (arbitrary units)');
ylabel('Y (arbitrary units)');
zlabel('Normalized E-field');
title('Objective 1: TEM Mode');
colorbar;
view(45,30);
grid on;

%% GRAPH 5: ELECTRIC FIELD DISTRIBUTION

subplot(3,3,5);
imagesc(x*1000,y*1000,E_field);
set(gca,'YDir','normal');
xlabel('X (mm)');
ylabel('Y (mm)');
title('Objective 2: Electric Field (TE_{10})');
colorbar;
axis tight;

%% GRAPH 6: MAGNETIC FIELD DISTRIBUTION

subplot(3,3,6);
imagesc(x*1000,y*1000,H_field);
set(gca,'YDir','normal');
xlabel('X (mm)');
ylabel('Y (mm)');
title('Objective 2: Magnetic Field (Illustrative)');
colorbar;
axis tight;

%% GRAPH 7: PROPAGATION CONSTANT VS FREQUENCY

subplot(3,3,7);
plot(freq/1e9,betaTEM,'LineWidth',1.5);
hold on;
plot(freq/1e9,betaTE10,'LineWidth',1.5);
plot(freq/1e9,betaTE20,'LineWidth',1.5);
plot(freq/1e9,betaTM11,'LineWidth',1.5);

xline(fc_TE10/1e9,'--','TE_{10} cutoff');
xline(fc_TE20/1e9,'--','TE_{20} cutoff');
xline(fc_TM11/1e9,'--','TM_{11} cutoff');

xlabel('Frequency (GHz)');
ylabel('\beta (rad/m)');
title('Objective 3: Propagation Constant');
legend('TEM','TE_{10}','TE_{20}','TM_{11}', ...
       'Location','northwest');
grid on;
xlim([1 30]);

%% GRAPH 8: CUTOFF FREQUENCY COMPARISON

subplot(3,3,8);

cutoffGHz = [fc_TEM fc_TE10 fc_TE20 fc_TM11]/1e9;

bar(cutoffGHz);
set(gca,'XTick',1:4);
set(gca,'XTickLabel',{'TEM','TE10','TE20','TM11'});

ylabel('Cutoff Frequency (GHz)');
title('Objective 3: Cutoff Frequencies');
grid on;

%% GRAPH 9: MODE PROPAGATION SUITABILITY

subplot(3,3,9);

bar(status);
set(gca,'XTick',1:4);
set(gca,'XTickLabel',{'TEM','TE10','TE20','TM11'});

ylim([0 1.2]);
yticks([0 1]);
yticklabels({'Below cutoff','Propagating'});

ylabel('Propagation Status');
title('Objective 4: Mode Suitability at 10 GHz');
grid on;

sgtitle('ECA13: TEM, TE and TM Electromagnetic Propagation Analysis');

%% DISPLAY NUMERICAL RESULTS

fprintf('\n========================================\n');
fprintf(' MICROWAVE ENGINEERING - EXPERIMENT 1\n');
fprintf('========================================\n');

fprintf('Operating Frequency = %.2f GHz\n',f0/1e9);

fprintf('\nCUTOFF FREQUENCIES\n');
fprintf('TEM  = 0 GHz (ideal two-conductor line)\n');
fprintf('TE10 = %.3f GHz\n',fc_TE10/1e9);
fprintf('TE20 = %.3f GHz\n',fc_TE20/1e9);
fprintf('TM11 = %.3f GHz\n',fc_TM11/1e9);

fprintf('\nPROPAGATION CONSTANTS AT %.2f GHz\n',f0/1e9);
fprintf('TEM  beta = %.3f rad/m\n',beta_TEM);
fprintf('TE10 beta = %.3f rad/m\n',beta_TE10);

if isnan(beta_TE20)
    fprintf('TE20 is below cutoff at %.2f GHz\n',f0/1e9);
else
    fprintf('TE20 beta = %.3f rad/m\n',beta_TE20);
end

if isnan(beta_TM11)
    fprintf('TM11 is below cutoff at %.2f GHz\n',f0/1e9);
else
    fprintf('TM11 beta = %.3f rad/m\n',beta_TM11);
end

fprintf('\nPROPAGATION STATUS AT %.2f GHz\n',f0/1e9);

if f0 > fc_TE10
    fprintf('TE10: Propagating\n');
else
    fprintf('TE10: Below cutoff\n');
end

if f0 > fc_TE20
    fprintf('TE20: Propagating\n');
else
    fprintf('TE20: Below cutoff\n');
end

if f0 > fc_TM11
    fprintf('TM11: Propagating\n');
else
    fprintf('TM11: Below cutoff\n');
end

fprintf('\nAll four objectives are represented in one figure.\n');

---
layout: default
title: Research
parent: Home
---

# Research

<video width="640" height="380" controls>
  <source type="video/mp4" src="{{site.baseurl}}/img/video/3D_viz_RatonBasinEQs_EAM.mp4"></source>
</video>

<!-- ![Alternate Text]({image-url})]({{{site.baseurl}}/img/video/3D_viz_RatonBasinEQs_EAM.mp4}) -->
  <!-- <source src="{{site.baseurl}}/img/video/3D_viz_RatonBasinEQs_EAM.mp4" type=video/mp4> -->
---
#### Code Snippet

In MATLAB, I made a 3D diagram of the earthquakes in my research field site. I used a clustering algorithm in MATLAB to index the different fault lineations.:

```py

%% Setup the Import Options and import the data
opts = delimitedTextImportOptions("NumVariables", 15);

% Specify range and delimiter
opts.DataLines = [2, Inf];
opts.Delimiter = ",";

% Specify column names and types
opts.VariableNames = ["year", "month", "day", "hour", "minute", "second", "lat", "lon", "depth", "north_sd", "east_sd", "depth_sd", "time_sd", "ML", "rms"];
opts.VariableTypes = ["double", "double", "double", "double", "double", "double", "double", "double", "double", "double", "double", "double", "double", "double", "double"];

% Specify file level properties
opts.ExtraColumnsRule = "ignore";
opts.EmptyLineRule = "read";


% Import the data
D = readtable("G:\Other computers\My Surfacebook 2 Laptop\Downloads\InverseTheory\raton_nakai_eqs.csv", opts);

% Clear temporary variables
clear opts

% Import Raton Basin Boundary
B=shaperead('rbbndg.shp','usegeo', true);
% mapshow(B)

% convert degrees to km
latb = B.Lat';
latb(370,:) = [];
lonb = B.Lon';
lonb(370,:) = [];
db = zeros(1,369)';

% assign data variables
lat=D(:,7);         % deg
lon=D(:,8);         % deg
depth=D(:,9);       % km
north_sd=D(:,10);   % deg
east_sd=D(:,11);    % deg
depth_sd=D(:,12);   % km
    % Standard deviations of north, east, depth and time are described
    % as well as origin parameters, magnitude, and rms.
    % Location method as described in this paper (Bayesloc) with velocity models as given in Table 2.
    % Event depth is given relative to local surface elevation.

% table2array
lat=table2array(lat);           % deg
lon=table2array(lon);           % deg
depth=table2array(depth);       % km
north_sd=table2array(north_sd); % km
east_sd=table2array(east_sd);   % km
depth_sd=table2array(depth_sd); % km

% convert degrees to km
x = 111.12*cos((pi/180)*mean(lat))*(lon-min(lon));
y = 111.12*(lat-min(lat));
xb = 111.12*cos((pi/180)*mean(lat))*(lonb-min(lon));
yb = 111.12*(latb-min(lat));
dobs=-depth;
N=length(-depth);

%% 3D plot of data
% use rotate button to examine it prom different perspectives!
figure(1);
clf;
set(gca,'LineWidth',3);
set(gca,'FontSize',14);
hold on;
pxmin=-25; pxmax=75;
pymin=-25; pymax=110;
pzmin=-30; pzmax=0;
axis( [pxmin, pxmax, pymin, pymax, pzmin, pzmax]' );
plot3(x,y,dobs,'ko','LineWidth',1);

% Draw boundary
hold on;
plot3(xb,yb,db,'k','LineWidth',2,'DisplayName','Basin Boundary');

%% Group event clusters
idx = clusterdata([x,y,dobs],10);
gu=unique(idx);
clrstr={'#0aff99','#ff8700','#ffff3f','#147df5','r','r','r','r','#be0aff','r'};
clr=clrstr(1,1:length(gu));
mrksle = {'.','.','.','.','x','x','x','x','.','x'};
mrksze  = 20;
mrkfclr = 'auto';
leg = false;
legloc = 'SouthEast';
hold on;
gscatter3(x,y,dobs,idx,clr,mrksle, mrksze,mrkfclr, leg, legloc)
hold on;
%% Draw 3D box
hold on;
plot3( [pxmin,pxmin], [pymin,pymin], [pzmin,pzmax], 'k-', 'LineWidth', 2 );
plot3( [pxmin,pxmin], [pymin,pymax], [pzmin,pzmin], 'k-', 'LineWidth', 2 );
plot3( [pxmin,pxmax], [pymin,pymin], [pzmin,pzmin], 'k-', 'LineWidth', 2 );

plot3( [pxmax,pxmax], [pymax,pymax], [pzmax,pzmin], 'k-', 'LineWidth', 2 );
plot3( [pxmax,pxmax], [pymax,pymin], [pzmax,pzmax], 'k-', 'LineWidth', 2 );
plot3( [pxmax,pxmin], [pymax,pymax], [pzmax,pzmax], 'k-', 'LineWidth', 2 );

plot3( [pxmax,pxmin], [pymin,pymin], [pzmax,pzmax], 'k-', 'LineWidth', 2 );
plot3( [pxmax,pxmax], [pymin,pymin], [pzmax,pzmin], 'k-', 'LineWidth', 2 );

plot3( [pxmin,pxmin], [pymax,pymin], [pzmax,pzmax], 'k-', 'LineWidth', 2 );
plot3( [pxmin,pxmin], [pymax,pymax], [pzmax,pzmin], 'k-', 'LineWidth', 2 );

plot3( [pxmax,pxmax], [pymax,pymin], [pzmin,pzmin], 'k-', 'LineWidth', 2 );
plot3( [pxmax,pxmin], [pymax,pymax], [pzmin,pzmin], 'k-', 'LineWidth', 2 );

%% LEGEND
legend('events','Basin Boundary', 'Cluster 1', 'Cluster 2','Cluster 3','Cluster 4','Cluster 5','Cluster 6','Cluster 7','Cluster 8','Cluster 9','Cluster 10');
% title('Fault Lineation Model')
xlabel('Latitude [km]')
ylabel('Longitude [km]')
zlabel('Depth [km]')
hold off;

%% RECORD
daspect([1,1,.3]);axis tight;
OptionZ.FrameRate=15;OptionZ.Duration=8;OptionZ.Periodic=false;
CaptureFigVid([0,90;0,90;-20,80;-40,60;-80,40;-100,20;-120,10;-140,10;-160,10;-180,10;...
    -200,10;-220,10;-240,10;-260,10;-280,10;-300,10;-320,10;-340,10;-360,10],'Results(1)_test',OptionZ)
```

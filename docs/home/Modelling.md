---
layout: default
title: Modelling
parent: Home
---

# Simple Integrated Geomorphological Numerical Model Simulations

<!-- <video width="640" height="380" controls>
  <source type="video/mp4" src="{{site.baseurl}}/img/video/SIGNUM.mp4"></source>
</video> -->
<img src="{{site.baseurl}}/img/gif/SIGNUM.gif"/>

---
The processes that control erosion cause geomorphological events that describe earth’s surface. This includes aspects of geology that explain the development of landforms and how those landforms are pieced together to form vast landscapes. By modeling the erosion process a better prediction of future geologic reconstruction can be developed to provide a clear understanding of Earth’s geomorphology. Main formulations of erosion in this study include stream channeling through bedrock, uplift and linear diffusion. Here I only show the uplift model. 

---
## Uplift
This function calculates height variations where $`u_{f}`$ is a parameter that I edited under default parameters.

---
#### Code Snippet

In MATLAB, I changed the parameters for uplift, stream power, and linear diffusion, to be able to better understand how to model the morphological processes of mountain building. SIGNUM or Simple Integrated Geomorphological Numerical Model simulates sediment transport and erosion by river flow, hill slope diffusion, fluvial incision and tectonic uplift. These scripts use the stream power equation, linear diffusion and uplift rates to analyze the effects it has in the SIGNUM model. A code snippet of the uplift function is show here:


```py
function dz = SIGNUM_uplift(uf,dt,buplift)
% This function can be used inside the SIGNUM model (see also SIGNUM.m) to
% simulate uniform uplift.
% Syntax:
%   dz = SIGNUM_uplift(uf,dt,buplift)
% Inputs:
%   points: (global) structure of surface points (see SIGNUM help for more info)
%   uf: uplift velocity (dz/dt) - can be an array of the same size as points
%   dt: time interval
%   buplift: flag controlling whether border points uplift or not
%    0 - all boundary points do not uplift,
%    1 - open boundary points (boundary=1) do not uplift,
%    2 - all boundary points uplift
%    (optional, default = 1)
% Outputs:
%   dz:      height variations

%     Copyright (C) 2011-2012  the authors
%
%     This program is free software: you can redistribute it and/or modify
%     it under the terms of the GNU General Public License as published by
%     the Free Software Foundation, either version 3 of the License, or
%     (at your option) any later version.
%
%     This program is distributed in the hope that it will be useful,
%     but WITHOUT ANY WARRANTY; without even the implied warranty of
%     MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
%     GNU General Public License for more details.

global points;

if nargin<3
    buplift=1;
end

if isscalar(uf); %uf = repmat(uf,length(points),1); end;
    %dz = zeros(length(points),1);
    % Speed-up code: different loops for different boundary conditions
    dz = dt .* uf .* ones(length(points),1);
    switch buplift
        case 0
            dz([points(:).border] ~= 0) = 0;
        case 1
            dz([points(:).border] == 1) = 0;
        otherwise
    end
else
    dz = zeros(length(points),1);
    % Speed-up code: different loops for different boundary conditions
    switch buplift
        case 0
            for k = 1:length(points)
                if (points(k).border == 0)
                    dz(k) = dt .* uf(k);
                end
            end
        case 1
            for k = 1:length(points)
                if (points(k).border ~= 1)
                    dz(k) = dt .* uf(k);
                end
            end
        otherwise
            for k = 1:length(points)
                dz(k) = dt .* uf(k);
            end
    end
end
```

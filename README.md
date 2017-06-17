# Vehicle_Detection

## Useful Code:
```
def slide_window(img, x_start_stop=[None, None], y_start_stop=[None, None], 
                    xy_window=(64, 64), xy_overlap=(0.5, 0.5)):
    # If x and/or y start/stop positions not defined, set to image size
    if x_start_stop[0] == None:
        x_start_stop = [0, len(img[0])]
        
    if y_start_stop[0] == None:
        y_start_stop = [0, len(img)]
        
    no_x_window = int(floor((x_start_stop[1]/(xy_window[0]/2))-1))
    no_y_window = int(floor((y_start_stop[1]/(xy_window[1]/2))-1))
    
    x_posns = []
    y_posns = []
    
    for i in range(no_x_window):
        x_posns.append(int(i*(xy_window[0]/2)))
        
    for i in range(no_y_window):
        y_posns.append(int(i*(xy_window[1]/2)))
        
    
        
    # Compute the span of the region to be searched    
    # Compute the number of pixels per step in x/y
    # Compute the number of windows in x/y
    # Initialize a list to append window positions to
    #window_list = []
    window_list = [(x,y]) for x in x_posns for y in y_posns]
    # Loop through finding x and y window positions
    #     Note: you could vectorize this step, but in practice
    #     you'll be considering windows one by one with your
    #     classifier, so looping makes sense
        # Calculate each window position
        # Append window position to list
    # Return the list of windows
    return window_list
```

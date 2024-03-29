

# Use Pandas to find and interact with handles, windows, and elements


### This library doesn't need Pywin32, it uses ctypes!


#### Inspired by [pywinauto](https://github.com/pywinauto/pywinauto), but [Pandas ](https://pandas.pydata.org/)is used to localize 


#### elements and call functions. Sounds weird, but it is 


#### extremely efficient and will save you a lot of time. Try it. 

##### Here is a vídeo (in Brazilian Portuguese)

[![YT](https://github.com/hansalemaos/screenshots/raw/main/pdwinauto/mcll.png)](https://www.youtube.com/watch?v=c3MaVFzqAvw)


[https://www.youtube.com/watch?v=c3MaVFzqAvw]()

```python
$pip install pdwinauto
# How to get the DataFrames

from pdwinauto import (mkey, get_window_frame, get_automation_frame, get_automation_frame_from_pid,
                  get_automation_frame_from_filenames, get_automation_frame_from_all_pids, )
mkey.enable_failsafekill("ctrl+e") #Always use a failsafe 

# Here are some way you can use to get the DataFrame
df1 = get_automation_frame_from_pid(pids=[12720, 3684], uia=True, screenshot_folder='f:\\screenshotstestx1', timeout=30)
df2 = get_automation_frame_from_filenames(["chrome.exe", "mspaint.exe"], uia=True, screenshot_folder=None, timeout=30)
df3 = get_automation_frame_from_all_pids(uia=True, screenshot_folder=None)
df4 = get_window_frame(pid=0)
df5 = get_automation_frame(df4,uia=True, screenshot_folder=None)
```



```python
# How to click on an element without moving the mouse:
# technically it is not a mouse click, but the results are the same 
# You might encounter elements that don't work with these methods:

# 1) Sometimes the results are better using:
df.iloc[1].ff_Invoke()
df1.dropna(subset='ff_Invoke').iloc[0].ff_Invoke() # another example

# 2) and other times, this function works better (Uses PostMessage)
df.d_loc_no_exception('str.contains','Cancel',na=False,flags=re.I).aa_direct_click.iloc[0]()
```



```python
# How to click on an element with the mouse cursor:
# 1)
# The click does always work since it uses the coordinates instead of the handle.
# You don't have to pass the coordinates. The module takes care of it. 
# Be careful: The function is executed at maximum speed. 
df.mm_left_click_xy_direct.iloc[0]()
# If you want, you can pass an offset (from the center):
df.mm_left_click_xy_direct.iloc[0](offset_x=100,offset_y=100)
#-------------------------------------------------------------------------------------------#
# 2) 
# If you prefer a slower, natural mouse movement:
df.mm_left_click_xy_natural.iloc[0](    
    delay=0.1, # duration of the mouse click (down - up)
    min_variation=-3, # a random value will be added to each pixel  - define the minimum here
    max_variation=3, # a random value will be added to each pixel  - define the maximum here
    use_every=4, # use every nth pixel
    sleeptime=(0.005, 0.009), # delay between each coordinate
    print_coords=True, # console output
    percent=90, # if you want to speed up the execution, lower this number
    offset_x=50, # offset from the center 
    offset_y=100,) # offset from the center 

# Also available for middle/right mouse click:
# Some examples:
df.mm_middle_click_xy_natural.iloc[0](offset_x=50, offset_y=100)
df.mm_right_click_xy_natural.iloc[0](offset_x=50, offset_y=100)
df.mm_right_click_xy_direct.iloc[0](offset_x=100,offset_y=100)
```



```python
# How to move the cursor to an element 
df.mm_move_to_natural.iloc[0](offset_x=50, offset_y=100) # takes longer 
df.mm_move_to_natural.iloc[0](use_every=10,offset_x=100, offset_y=100) # if you want to speed it up, lower the value of use_every
df.mm_moveto.iloc[0](offset_x=50, offset_y=100) # immediately
```



```python
# How to save all screenshots to the HDD:
df1.screenshot_save.apply(lambda x:x())
```



```python
# How to send keystrokes 
# 1) 
# The Window cannot be minimized if you use this function. 
# You can find all valid keys here: mkey.show_all_keys,
df.mm_press_key.iloc[0]('b',delay=1) 
#-------------------------------------------------------------------------------------------#
# 2)
# This function works when the window is minimized,
# it restores the window, and sets focus, but it also moves the mouse.
df.mm_press_key_force_activate.iloc[0]('a',1)
#-------------------------------------------------------------------------------------------#
# 3)
# Code based on:
# https://pywinauto.readthedocs.io
# The key codes are the same. 
df.mm_send_keystrokes.iloc[0]('%{F4}') # Closing the window: '%{F4}' (alt+f4)
#-------------------------------------------------------------------------------------------#
# 4) Send as many keystrokes as you want simultaneously
# Use force activate to make sure that the strokes go to the right window
df.mm_press_keys_simultaneously.iloc[0](["alt", "f4"], 1.1) 
df.mm_press_keys_simultaneously_force_activate.iloc[0](["alt", "f4"], 1.1) # better 
#-------------------------------------------------------------------------------------------#
# 5) Send Unicode
df.mm_send_unicode.iloc[0]('HGax&&öp')
# Use force activate to make sure that it goes to the right window
df.mm_send_unicode_force_activate.iloc[0]('HGax&&öp')
```



```python
# How to activate (set focus to) a window 

# works most of the time and doesn't change mouse position
df.mm_activate_window.iloc[0]()
#-------------------------------------------------------------------------------------------#

# This function has been working for me without any problem. 
# It always activates the window and sets focus, 
# But it changes the mouse position
df.mm_force_activate_window.iloc[0]()

# You can also try this:
df.ff_Select.iloc[0](1)
```


### More useful stuff:


```python
# How to close the window/app
df.dropna(subset='ff_Close').iloc[0].ff_Close()
##############################################################################################
# move the window to (10,10) without resizing
df.dropna(subset='ff_Move').iloc[1].ff_Move(10,10)
##############################################################################################
# get a DataFrame with all children from an element:
df.dropna(subset='aa_all_children').iloc[1].aa_show_children()
##############################################################################################
# If you want to search in the whole DataFrame without getting any Exception ;-) :
df.d_loc_no_exception('str.contains','Cancel',na=False,flags=re.I).aa_direct_click.iloc[0]()
##############################################################################################
# maximize a window
df.window_maximize.iloc[0]()
##############################################################################################
# minimize a window
df.window_minimize.iloc[0]()
##############################################################################################
# restore windows
df.window_restore.iloc[0]()
##############################################################################################
# hide window
df.window_hide.iloc[0]()
##############################################################################################
# show window
df.window_show.iloc[0]()
##############################################################################################
# window to normal size
df.window_normal.iloc[0]()
##############################################################################################
# force minimize
df.window_forceminimize.iloc[0]()
##############################################################################################
# resizing a window:
df.iloc[0].resize_window( (0,0,100,100))
##############################################################################################
```



## Here is an example with Notepad:




```python
from pdwinauto import (
   mkey,
   click_on_main_menu,
   click_on_submenu,
   get_automation_frame_from_pid,
   sleep
)
mkey.enable_failsafekill("ctrl+e")
n1,n2 = "&File","&Save\tCtrl+S"
filepath = "F:\\tesa\\testtext1.txt"
text = "Hallo, mein Freund, wie geht es dir?"
pid = 11660
gdf = lambda: get_automation_frame_from_pid(pid)
df = gdf()
df.mm_send_unicode_force_activate.iloc[0](text)
sleep(1)
df4 = df.dropna(subset="aa_menu_items")
click_on_main_menu(df4[:1], menu1text=n1)
sleep(1)
df2 = gdf()
click_on_submenu(df2.dropna(subset="aa_menu_items"
                           ), menu1text=n1, menu2text=n2)
while True:
   sleep(2)
   try:
       df = gdf()
       f1 = df.d_loc_no_exception("str.contains",r"\.txt",
                                  regex=True)
       f2 = f1.d_loc_no_exception("str.contains",r"Edit")
       f2.iloc[0].mm_send_unicode_force_activate(filepath)
       sleep(2)
       df.loc[df.ff_CurrentDefaultAction ==
              "Save"].iloc[0].ff_DoDefaultAction()
       sleep(1)
       (df.loc[~df.ff_Close.isna() &
              (df.aa_title == 'Notepad')]
        .iloc[0].ff_Close())
       break
   except Exception as fe:
       continue
```



<img title="" src="https://github.com/hansalemaos/screenshots/raw/main/pdwinauto/00000000.png" alt="">

<img title="" src="https://github.com/hansalemaos/screenshots/raw/main/pdwinauto/00000001.png" alt="">

<img title="" src="https://github.com/hansalemaos/screenshots/raw/main/pdwinauto/00000002.png" alt="">

<img title="" src="https://github.com/hansalemaos/screenshots/raw/main/pdwinauto/00000003.png" alt="">

<img title="" src="https://github.com/hansalemaos/screenshots/raw/main/pdwinauto/00000004.png" alt="">

<img title="" src="https://github.com/hansalemaos/screenshots/raw/main/pdwinauto/00000005.png" alt="">

<img title="" src="https://github.com/hansalemaos/screenshots/raw/main/pdwinauto/00000006.png" alt="">


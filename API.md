jythonSWT API
=============

__Note__: This API should match the jythonSwing API.  If there is discrepancies between these two APIs, the jythonSWT implementation should be modified to match jythonSwing.

Listeners
=========

These listener classes are intended to be hidden from the end-user.  These classes provide the bridge between the user-defined widget, Swing Listeners, and the user-defined event handler.  These listeners are created by the widgets themselves, so the user simply needs to pass the event handler function to the widget during construction.

These are default listeners associated with the standard Swing widgets defined below to automatically generate the desired respones to an event, or ease defining an event handler by passing a function name to the constructor of the widget.


ButtonListener
--------------

Listener for the Button, CheckBox and DropDownList widgets.  eventHandler is called when the Button or CheckBox is clicked, or a selection is made by the DropDownList.

### __init__(self, eventFunction)

Points this listener to eventFunction when the button is pressed

### actionPerformed(self, event)

Call the eventFunction


SliderListener
--------------

Listener for the Slider widget.  eventHandler is called when the slider is moved - it is passed the current value of the slider.

### __init__(self, slider, eventFunction)

Points this listener to eventFunction when something happens


### stateChanged(self, event)

Call the eventFunction


DropDownListListener
--------------------

Listener for the DropDownList widget.  eventHandler is called when an item is selected - it is passed the item.

### __init__(self, dropDownList, eventFunction)

Points this listener to eventFunction when something happens

### actionPerformed(self, event

Call the eventFunction


TextFieldListener
-----------------

Listener for the TextField widget.  eventHandler is called when the user types the Enter key.

### __init__(self, textField, eventFunction)

Points this listener to eventFunction when something happens

### actionPerformed(self, event)

Call the eventFunction


MenuItemListener
----------------

Listener for a menu item being selected

### __init__(self, callbackFunction)

Point this listener to the callback function when the menuitem is selected.

### actionPerformed(self, event)

Call the callback funciton


KeyboardListener
----------------

Listener for keyboard events.  This listener only provides functionality for when a key is typed.  For the purpose of simplicity, there is no event handling for when a key is pressed (and held down), or released.

### Constants

SHIFT_KEY	0
CONTROL_KEY	1
ALT_KEY		2
META_KEY	3

### __init__(self)

Points this listener to the eventFunction when something happens

### nullFunctionCharacterOnly(self, character)

nullFunction does nothing, and accepts a character.

### nullFunctionCharacterPlusModifiers(self, character, modifierKeysState)

nullFunction does nothing, and accepts a character, and the state of the four modifier keys, a list of four booleans, each for shiftDown, controlDown, altDown, metaDown in this order.

### keyTyped(self, keyEvent)

When a key is typed

### keyPressed(self, keyEvent)

When a key is pressed

### keyReleased(self, keyEvent)

When a key is released


MouseClickListener
------------------

Listener for mouse button events.  This listener only provides functionality for when either mouse button is clicked.  For simplicity, there is no event handling for when the mouse button is pressed (and held), or released.  Nor is there event handling for when the mouse is located within the context (i.e., widget) of this listener.

### __init__(self, remapCoordinates)

### nullFunction(self, x, y)

nullFunction does nothing, accepts mouse coordinates

### mousePressed(self, mouseEvent)

Mouse button is pressed (but not yet released)

### mouseReleased(self, mouseEvent)

Mouse button is released

### mouseEntered(self, mouseEvent)

Mouse has entered the boundary of the object

### mouseExited(self, mouseEvent)

Mouse has exited the boundary of the object

### mouseClicked(self, mouseEvent)

Mouse button has been clicked.   Call the click function.


MouseMovementListener
---------------------

Listener for mouse motion events.  This listener passes the coordinates of the mouse to the event handling function whenever the mouse is moved.  Mouse movement (i.e., with no mouse button pressed) and mouse dragging (i.e., when a mouse button is held down) are treated the same.

### __init__(self, remapCoordinates)

### nullFunction(self, x, y)

nullFunction does nothing, accepts mouse coordinates.

### mouseMoved(self, mouseEvent)

Pass the new location to the handler

### mouseDragged(self, mouseEvent)

Pass the new location to the handler



# DisplayListener
#
# Listener for closing the display.  Allows user to add a callback function
# to clean up still-running code.

class DisplayListener(WindowListener):
   """
   Listener for closing the window
   """

   def __init__(self, parentFrame):

   def nullFunction(self):

   def windowActivated(self, event):

   def windowClosed(self, event):

   def windowClosing(self, event):

   def windowDeactivated(self, event):

   def windowDeiconified(self, event):

   def windowIconified(self, event):

   def windowOpened(self, event):
      

# ComponentChangeListener
#
# Used to automatically update components when resized or moved.
# This is needed, since changing the component's position or size 
# does not automatically change the Java Swing representation of the
# component's size or position

class ComponentChangeListener(ComponentListener):
   """
   Listener for when components change
   """
   
   def __init__(self):
      """	  
      """	  

   def componentHidden(self, event):
      """
      """	  
	  
   def componentMoved(self, event):
      """	  
      """

   def componentResized(self, event):
      """	  
	  """
	  
   def componentShown(self, event):
      """	  
      """



-----------------------

###############################################################################
# gui.py        Version 2.1   15-Feb-2013
# Dana Hughes, Bill Manaris, and Kenneth Hanson
#
# This library is based on Java's Swing GUI library.  It can be used to build
# GUIs in jython.  The purpose of this library is to provide a cleaner, simpler,
# easier-to-use alternative to Swing.  The gui.py library keeps simple things simple,
# and makes complicated things possible.  
#
# This package imports the Swing Java packages, and provides factory functions
# for creating widgets with event handlers (functions) passed as parameters.
# The concept of Java classes and listeners are hidden from the end-user (GUI programmer).  
# Only event handling functions (callbacks) need to be defined.
#
# REVISIONS:
#
#   2.1     15-Feb-2013 (bzm) Added Oval(); also Display.add(), move(), and delete().
#                       Note that Display.delete() completely deletes item (i.e., it not only removes it
#                       from the display, but also deletes it, for memory management).
#                       Removed thickness from Drawable, as it is very hard to impelement correctly
#                       (i.e., without cropping) given our inversion of the y-axis (i.e., having
#                       orgigin (0,0) at bottom, as opposed to top.
#
#   2.01    19-Jan-2013 (bzm) Added JEM working directory fix (minor issue).
#
#   2.00    15-Dec-2012 (bzm) When an item is moved (placed) from one display to another, it is
#                       explicitly removed from the first one.
#                       Added set/getColor(), set/getThickness(), set/getFill() for drawable
#                       graphics objects (e.g., Line, Circle, etc.) via Drawable superclass.
#                       Setting one of those attributes automatically redraws object.
#                       Finalized API for 2.0 release.
#
#   1.89    06-Dec-2012 (bzm) Simplified menu creation - see addItem() and addItemList().
#                       Now, there is no need to create a menuItem anymore - we cut to the chase,
#                       i.e., we create a menu, add items to it (i.e., a string (name) and a function to call
#                       when item is selected), and add menu to the display.  Display updates itself
#                       to show new menus.  Menus appear on existing menu bar (no need to create one).
#                       So, now we just build a menu and add it.  Simpler, faster.
#   1.88    26-Nov-2012 (bzm) Tested all regular Widgets (and added sample unit tests).
#   1.87    18-Nov-2012 (bzm) Changed Dislay() to put new object in the display at beginning
#                       of z-order list - add() at index 0.
#   1.86    07-Nov-2012 (bzm) Changed Dislay() to use the default JFrame's content pane.
#                       This seems to fix the problem with a "dead" display area 
#                       at the bottom (lowest 20 pixels or so).
#   1.85    01-Nov-2012 (bzm) Refactored common code of widgets and graphics objects 
#                       into class Widget().
#   1.84    31-Oct-2012 (dth) Fixed bug with resizing widgets and graphics objects.
#   1.83    24-Oct-2012 (bzm) Image class was renamed to Icon (to fix conflict with image.py).
#                       The two image classes are incompatible enough to keep separate.
#   1.82    24-Oct-2012 (bzm) Image is created without specifying an initial position.
#                       Positioning is handled when placing() the image on a Display.
#   1.81    12-Oct-2012 (bzm) Slider callback function now expects one parameter, the slider value
#   1.8     21-Sep-2012 (bzm) Fixed problem with adding submenus (they needed special treatment).
#                       (But what about PopUpMenu's?  Can they have submenus?  If so,
#                        we need to handle that as well.)
#                       Fixed problem with KEY_RELEASED.  Now we capture status of modifier keys.
#                       Also, we properly report the key pressed/released/typed (to the best
#                       of our abilities - some keys are modified in the keyboard driver,
#                       before they get to us - but everything else, we now can handle).
#   1.7     19-Sep-2012 (bzm) Drawable objects are now specified using absolute coordinates
#                  (e.g., Line(x1, y1, x2, y2), etc. 
#                  Also, fixed minor bug with resizing Image() using only height argument.
#                  Also, fixed Display keyboard listener (now, keyboard events can be handled). 
#   1.6     14-Sep-2012 (bzm) Added Display.setToolTipText() to use Display's internal JPanel.
#                  Fixed Display.getHeight() bug to return the height specified in constructor.
#                  Commented out Display.draw(), erase() - legacy code (to be removed?)
#   1.5     11-Sep-2012 (bzm) Added a Point class.
#   1.4     05-Sep-2012 (bzm) Renamed module as 'gui'.
#	1.3		01-Jun-2012	Add menus and resize shapes.
#	1.2.2	01-Jun-2012	Fixed Image to be scalable.
#	1.2.1	21-May-2012	Added onClose.  Modified instantiating event listeners in shapes 
#                       to wait until a callback function is provided (to allow events to fall
#   					through if not handled by the shape).
#   1.2     16-May-2012 Implemented API changes.
#   1.1.1   14-May-2012 Modified mouse click to pass x and y coordinates of mouse to 
#                       user-defined function.
#
#
# TO DO:
#
# Currently, when resizing a label, it moves.  It should not.  Check remapping of coordinates
#   in Display.
#
###############################################################################

import sys

###############################################################################
# Swing Javax Packages
###############################################################################

# Is this needed?  It causes a naming conflict with jMusic's View class
#from javax.swing.text import *


###############################################################################
# Color
#
# Create a new color consisting of R, G, B (each 0-255), to be used with Widgets.
#
# for example:
#
# color = Color(255, 255, 255)    # same as Color.WHITE
#
# Also, use getRed(), getGreen(), getBlue()
#


###############################################################################
###############################################################################






###############################################################################
# Widget
#
# Class to encapsulate common functionality (mainly event handling) for 
# widget objects.
#

class Widget:
   """
   This class defines common GUI widget functionality.
   """

   def __init__(self):
      """
      Set up instance variables.
      """

   def __remapCoordinates__(self, x, y):
      """
      Adjust the coordinates so that y is in the lower-left corner
      """

   def onKeyType(self, callbackFunction):
      """
      Set up a callback function for when a key is typed.
      """
      
   def onKeyDown(self, callbackFunction):
      """
      Set up a callback function for when a key is pressed.
      """


   def onKeyUp(self, callbackFunction):
      """
      Set up a callback function for when a key is released.
      """


   def onMouseClick(self, callbackFunction):
      """
      Set up a callback function for when the mouse is clicked.
      """


   def onMouseDown(self, callbackFunction):
      """
      Set up a callback function for when the mouse button is pressed.
      """


   def onMouseUp(self, callbackFunction):
      """
      Set up a callback function for when the mouse button is released.
      """


   def onMouseEnter(self, callbackFunction):
      """
      Set up a callback function for when the mouse enters the display.
      """

   def onMouseExit(self, callbackFunction):
      """
      Set up a callback function for when the mouse exits the display.
      """


   def onMouseMove(self, callbackFunction):
      """
      Set up a callback function for when the mouse is moved.
      """


   def onMouseDrag(self, callbackFunction):
      """
      Set up a callback function for when the mouse is dragged.
      """


   def addPopupMenu(self, menu):



###############################################################################
# Drawable
#
# Class to encapsulate common functionality (mainly color, fill, thickness) for 
# drawable gaphics objects.
#

class Drawable:
   """
   This class defines common GUI drawable object functionality.
   """

   def __init__(self, color = Color.BLACK, fill = False):
      """
      Create a drawable object.
      """
 
   def setColor(self, color = None):
      """
      Change the color of the drawable object.  If no color provided, use dialog box to select.
      """

   def getColor(self):
      """
      Returns the color of the drawable object.
      """

   def setFill(self, fill = True):
      """
      Change the fill state of the drawable object.
      """

   def getFill(self):
      """
      Returns the fill state of the drawable object.
      """

#   def setThickness(self, thickness):
#      """
#      Change the thickness of the drawable object.
#      """
#
#      self.thickness = thickness
#
#      if self.display:
#         self.display.contentPane.repaint()
#
#   def getThickness(self):
#      """
#      Returns the thickness of the drawable object.
#      """
#      return self.thickness



###############################################################################
# Display
#
# Class for generating a GUI window.  A program may open several Displays.  
# Extends Swing's JFrame class.
#
# Methods:
#
# Display()
# Display(title)
# Display(title, width, height)
#   Creates a new Display.
#   --title - Gives the Display a Title (displayed at the top of the window)
#   --width - The width (in pixels) of the Display window.
#   --height - The height (in pixels) of the Display window.
#
# show()
#   Displays the window.  
#
# hide()
#   Hide the window.
#
# add(widget)
#   Adds a widget to the Display.  Widgets are positioned using FlowLayout.
#
# NOTE:  This class was originally called Window, but was renamed for simplicity
# due to the presence of a Window class in jMusic.
###############################################################################

class Display():
   """
   GUI Window to hold widgets.
   """

   def __init__(self, title = "", width = 600, height = 400):
      """
      Create a new window.
      """



   def show(self):
      """Shows the display."""

   def hide(self):
      """Hides the display."""

   def __remapCoordinates__(self, x, y):
      """
      Adjust the y coordinate so that the origin is the lower-left corner, and
      y increases in the up direction.
      """

   def place(self, item, x=None, y=None):
      """
      Place an object in the display, at coordinates by x and y.
      If the object already appears on another display it is removed from there, first.
      """

   def add(self, item, x=None, y=None):
      """
      Same as place(), i.e., places an object in the display, at coordinates by x and y.
      If the object already appears on another display it is removed from there, first.
      """

   def reposition(self, item, x, y):
      """
      Change an object's location in the display
      """
      
   def move(self, item, x, y):
      """
      Same as reposition(), i.e., changes an object's location in the display
      """

   def remove(self, item):
      """
      Remove the item from the display.
      """
 
   def delete(self, item):
      """
      Completely deletes item - also removes the item from the display.
      """
 
   # now, some other useful functions
   def setToolTipText(self, text):
      """
      Sets the tooltip text of the Display's internal JPanel (since JFrame 
      does NOT have tooltip capabilities.
      """
      
   def getSize(self):
      """
      Uses the size reported by the Display's internal JPanel (since JFrame 
      overestimates height - it seems to add the height of the toolbar).
      """

   def getHeight(self):
      """
      Uses the height reported by the Display's internal JPanel (since JFrame 
      overestimates height - it seems to add the height of the toolbar).
      """

   def getWidth(self):
      """
      Uses the width reported by the Display's internal JPanel.
      """

   def addMenu(self, menu):
      """
      Add a menu to the menu bar
      """

   def addPopupMenu(self, menu):
      """
      Add a popup menu to this display
      """

   def onKeyType(self, callbackFunction):
      """
      Set up a callback function for when a key is typed.
      """

   def onKeyDown(self, callbackFunction):
      """
      Set up a callback function for when a key is pressed.
      """

   def onKeyUp(self, callbackFunction):
      """
      Set up a callback function for when a key is released.
      """

   def onMouseClick(self, callbackFunction):
      """
      Set up a callback function for when the mouse is clicked.
      """

   def onMouseDown(self, callbackFunction):
      """
      Set up a callback function for when the mouse button is pressed.
      """

   def onMouseUp(self, callbackFunction):
      """
      Set up a callback function for when the mouse button is released.
      """

   def onMouseEnter(self, callbackFunction):
      """
      Set up a callback function for when the mouse enters the display.
      """

   def onMouseExit(self, callbackFunction):
      """
      Set up a callback function for when the mouse exits the display.
      """

   def onMouseMove(self, callbackFunction):
      """
      Set up a callback function for when the mouse is moved.
      """

   def onMouseDrag(self, callbackFunction):
      """
      Set up a callback function for when the mouse is dragged.
      """

   def onClose(self, callbackFunction):


###############################################################################
# Menu
#
# Class for creating a menu (for use as a popup menu or with a menubar).
#
# Methods:
#
# Menu()
#   Creates a new Menu.  It uses either Swing's JMenu or JPopupMenu
#
# addMenuItem(menuItem)
#   Adds an item to the menu.  The name of the menu item is what is displayed
#   in the menu, and the callbackFunction is called when selected.
###############################################################################

class Menu:
   """
   Create a menu (either for use in a menubar, or as a popup)
   """

   def __init__(self, name):
      """ 
      Create a new menu.
      """

   def enable(self):
      """
      Enable the menu
      """

   def disable(self):
      """
      Disable the menu
      """

   def addItem(self, item = "", eventFunction = None):
      """
      Add an item to the menu.
      """

   def addItemList(self, itemList = [""], eventFunctionList = [None]):
      """
      Add a list of items and corresponding callback functions to the menu.
      """      

   def addSubMenu(self, menu):
      """
      Add a (sub)menu as an item to the menu.
      """
      self.menuItems.append(menu)  

   def addSeparator(self):
      """
      Add a separator to the menu
      """
      self.menuItems.append("SEPARATOR")

   # Deprecated in favor of new, simpler way (see addItem() and addItemList()).
   # Still useful, however, if we need to disable a single menu item (this way the end-programmer
   # maintains a handle to a particular menu item).
   def addMenuItem(self, menuItem):   
      """
      Add an item to the menu.
      """

   def __toJMenu__(self):
      """
      Return a JMenu version of this menu
      """

   def __toJPopupMenu__(self):
      """
      Return a JPopupMenu version of this menu
      """


###############################################################################
# MenuItem
#
# Class for create a new menu item
#
###############################################################################

class MenuItem(JMenuItem):
   """
   A single entry for a menu
   """

   def __init__(self, name, callbackFunction):
      """
      Create a new menu item
      """


###############################################################################
# Label
#
# Class for creating text labels.  An extention of Swing's JLabel.
#
# Methods:
#
# Label(text)
# Label(text, alignment)
#   Create a new label containing the text.  The alignment may be LEFT,
#   CENTER (default), or RIGHT.
###############################################################################

LEFT = SwingConstants.LEFT
CENTER = SwingConstants.CENTER
RIGHT = SwingConstants.RIGHT

class Label(JLabel, Widget):
   """
   A widget to contain text.
   """

   def __init__(self, text, alignment = LEFT):
      """
      Create the text label
      """
      

###############################################################################
# Button
#
# Class for creating pushbuttons.  Extends Swing's JButton class.
#
# Methods:
#
# Button(label, event)
#   Creates a new pushbutton widget.
#   --label - A text label to be placed on the button.
#   --event - The user-defined event-handling function called when this button
#             is clicked.
###############################################################################

class Button(JButton, Widget):
   """
   Push Button
   """

   def __init__(self, label, eventFunction):
      """
      Create a new button
      """


###############################################################################
# Checkbox
#
# Class for creating checkboxes.  Extends Swing's JCheckBox class.
#
# Methods:
#
# Checkbox()
# Checkbox(label)
# Checkbox(label, event)
#   Creates a new checkbox.
#   --label - A text label placed to the right of the checkbox
#   --event - The user-defined function called when this checkbox is checked or unchecked.  This is optional.
#
# isChecked()
#   Returns True if the Checkbox is checked, False if unchecked.
#
# check()
#   Sets the Checkbox to checked.
# uncheck()
#   Sets the Checkbox to unchecked.
###############################################################################

class Checkbox(JCheckBox, Widget):
   """
   Checkbox
   """

   def __init__(self, label="", eventFunction = None):
      """
      Create a new checkbox
      """

   def isChecked(self):
      """
      Return True if this box is checked
      """

   def check(self):
      """
      Sets the checkbox to checked. This does NOT cause 'eventFunction' (if any) to be called.
      """


   def uncheck(self):
      """
      Sets the checkbox to unchecked. This does NOT cause 'eventFunction' (if any) to be called.
      """


###############################################################################
# Slider
#
# Class for creating sliders.  Extends Swing's JSlider class.
#
# Methods:
#
# Slider()
# Slider(orientation)
# Slider(orientation, lower, upper, start)
# Slider(orientation, lower, upper, start, eventHandler)
#   Creates a new slider.
#   --orientation - HORIZONTAL or VERTICAL.  By default, HORIZONTAL
#   --lower - An integer representing the lowest value the slider may represent.  
#              By default, 0.
#   --upper - An integer representing the highest value the slider may represent.  
#             By default, 100.
#             NOTE: lower and upper are inclusive.
#   --start - The beginning position of the slider.  By default, 50.
#   --eventHandler - The user-defined function, which is called whenever the
#             slider is changed.  This function would need to use the getValue()
#            function to get the new value of the slider.
#
# getValue()
#   Returns the current value of the slider.
###############################################################################

# Some constants

HORIZONTAL = JSlider.HORIZONTAL
VERTICAL = JSlider.VERTICAL

class Slider(JSlider, Widget):
   """
   Slider
   """

   def __init__(self, orientation = HORIZONTAL, lower = 0, upper = 100,
                start = None, eventHandler = None):
      """
      Create a new Slider
      orientation - HORIZONTAL or VERTICAL
      lower - the lowest value the slider may take
      upper - the highest value the slider may take
      start - the beginning point of the slider
      """


###############################################################################
# DropDownList
#
# Class for adding a drop-down list of items, one of which can be selected.  
# Extends JComboBox.
#
# Methods:
#
# DropDownList()
#   Create a new drop-down list.
#
# addItem(item)
#   Adds a new item to the drop-down list.
#   --item - a string.
#
# addItemList(itemList)
#   Adds a list of strings to the drop-down list (instead of adding them one at
#   a time with addItem()).
#   --itemList - a list of items (strings) to be displayed in the drop-down list.
#
# getSelectedItem()
#   Returns the value of the currently selected item in this box (as a string).
###############################################################################

class DropDownList(JComboBox, Widget):
   """
   Drop-down List
   """
   def __init__(self, items = [""], eventHandler = None):
      """
      Create a new drop-down list
      """

   def __addItemList__(self, items):
      """
      Adds a list of items to the drop-down list
      """


###############################################################################
# Font
#
# For example,
#
# Font("Serif", Font.ITALIC, 16)
#
# See http://docs.oracle.com/javase/tutorial/2d/text/fonts.html#logical-fonts


###############################################################################
# TextField
#
# Class for adding an input text field.  Extends Swing's JTextField class.
#
# Methods:
#
# TextField()
# TextField(text)
# TextField(text, columns)
# TextField(text, columns, eventHanlder)
#   Creates an empty text field with the specified initial text (a string),
#   and number of columns (a string).  Default is "" (empty string) and 8.
#
#
# eventHandler, if present, is passed the text entered by user (without the newline character
#   that trigger the call.
#
# setFont(), e.g., setFont( Font("Serif", Font.ITALIC, 16) )
#
# getText()
#   Returns the text contained in the text field (as a string).
#
# setText()
#   Sets the text contained in the text field (as a string).
###############################################################################

class TextField(JTextField, Widget):
   """
   Text field
   """

   def __init__(self, text = "", columns = 8,  eventHandler = None):
      """
      Create a new text field
      """


###############################################################################
# TextArea
#
# Class for adding an input text area.  Extends Swing's JTextArea class.
#
# Methods:
#
# TextArea()
# TextArea(text)
# TextArea(text, rows, columns)
#   Creates an empty text field with the specified initial text (a string),
#   and number of rows and columns.  Default is "" (empty string) and 5, 40.
#
# setFont(), e.g., setFont( Font("Serif", Font.ITALIC, 16) )
#
# getText()
#   Returns the text contained in the text field (as a string).
#
# setText()
#   Returns the text contained in the text area (as a string).
#
# setLineWrap()
#   Sets whether the text area will wrap lines or not.  If the setting is off, 
#   then a horizontal scroll bar will appear, as needed.
###############################################################################

class TextArea(JScrollPane, Widget):
   """
   Text area (provides vertical scrolling, as needed.)
   """

   def __init__(self, text = "", rows = 5, columns = 40):
      """
      Create a new text area
      """

   def setLineWrap(self, setting = True):
      """
      Sets whether the text area will wrap lines or not.  If the setting is off, 
      then a horizontal scroll bar will appear, as needed.
      """


###############################################################################
# Drawable Objects
#
# These classes are all used by Display's paint method.  The paint method will
# call the paint method of each of these to create an image on the display
#

# Line
#
# Creates a line to be added to a display.

class Line(JPanel, Widget, Drawable):
   """
   A simple line specified through its end points.
   """

   def __init__(self, x1, y1, x2, y2, color = Color.BLACK):
      """
      Create a new line
      """

   def paint(self, graphics2DContext):
      """
      Paint me on the display
      """


# Oval
#
# Creates an oval to be added to a display.

class Oval(JPanel, Widget, Drawable):
   """
   A simple oval
   """

   def __init__(self, x1, y1, x2, y2, color = Color.BLACK, fill = False):
      """
      Create a new oval
      """

   def paint(self, graphics2DContext):
      """
      Paint me on the display
      """


# Circle
#
# Creates a circle to be added to a display.

class Circle(JPanel, Widget, Drawable):
   """
   A simple circle
   """

   def __init__(self, x, y, radius, color = Color.BLACK, fill = False):
      """
      Create a new circle
      """
 

   def paint(self, graphics2DContext):
      """
      Paint me on the display
      """


# Point
#
# Creates a point to be added to a display.

class Point(Circle):
   """
   A simple point
   """

   def __init__(self, x, y, pointColor=Color.BLACK):
      """
      Create a new point
      """


# Rectangle
#
# Creates a rectangle to be added to a display.

class Rectangle(JPanel, Widget, Drawable):
   """
   A simple rectangle specified by bottom-left and top-right corners.
   """

   def __init__(self, x1, y1, x2, y2, color = Color.BLACK, fill = False):
      """
      Create a new rectangle
      """

   def paint(self, graphics2DContext):
      """
      Paint me on the display
      """

######################################################################################
# JEM working directory fix
#
# JEM (written partially in Java) does not allow changing current directory.
# So, when we have the user's desired working directory we CANNOT use it to read/write
# jMusic media files, unless we add it as a prefix here to every Read/Write operation.
# We do so only if the filepath passed to Read/Write is just a filename (as opposed
# to a path).
#
# Let's define some useful stuff here, for this fix

def fixWorkingDirForJEM( filename ):
   """It prefixes the provided filename with JEM's working directory, if available,
      only if filename is NOT an absolute path (in which case the user truly knows
      where they want to store it).
   """
   
      
        
# Icon
#
# Creates an icon to be added to a display specifying its left-bottom corner.

class Icon(JPanel, Widget):
   """
   """
 
   def __resizeIcon__(self, width, height):
      """
      Returns a scaled version of the icon.
      """

   def setSize(self, width, height):
      """
      Change the size of this icon.
      """

   def paint(self, graphicsContext):
      """
      Paint me on the display.
      """

   

                  

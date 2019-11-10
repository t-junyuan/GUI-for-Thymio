# GUI-for-Thymio
GUI for attendance taking robot
from libdw import pyrebase
import random
from kivy.app import App
from kivy.uix.floatlayout import FloatLayout
from kivy.uix.gridlayout import GridLayout
from kivy.uix.label import Label
from kivy.uix.textinput import TextInput
from kivy.uix.button import Button
from kivy.uix.screenmanager import ScreenManager, Screen
from kivy.graphics import Color, Rectangle
from kivy.uix.scrollview import ScrollView
from kivy.uix.image import AsyncImage


### SET UP FIREBASE ###

url = "https://tim-by-design.firebaseio.com/"
apikey = "AIzaSyAlT5K9Dq9DEmDyKG4A7BV_d5AEMyPnJtU"

config = {
    "apiKey": apikey,
    "databaseURL": url,
}

firebase = pyrebase.initialize_app(config)
db = firebase.database() 


### STANDARD FORMATTING ###

class DarkButton(Button):
    
    def __init__(self, **kwargs):
        Button.__init__(self, **kwargs)
        
        # Formatting the text in the button
        self.font_size = 30
        self.halign = "center"
        self.valign = "middle"
        self.color = (1, 1, 1, 1)
        
        # Formatting the colour of the button
        self.background_normal = "" 
        self.background_color = (0/255, 155/255, 229/255, 0.6)

    
class LightButton(Button):
    
    def __init__(self, **kwargs):
        Button.__init__(self, **kwargs)
        
        # Formatting the text in the button
        self.font_size = 30
        self.halign = "center"
        self.valign = "middle"
        self.color = (1, 1, 1, 1)
        
        # Formatting the colour of the button
        self.background_normal = ""
        self.background_color = (0/255, 155/255, 229/255, 1)


class DarkLabel(Label):
    
    def __init__(self, **kwargs):
        Label.__init__(self, **kwargs)
        
        # Formatting the text in the label
        self.bind(size = self.setter("text_size"))
        self.padding = (20, 20)
        self.font_size = 30
        self.halign = "center"
        self.valign = "middle"
        self.color = (1, 1, 1, 1)

    def on_size(self, *args):
        # Formatting the colour of the label, by placing a rectangle behind it
        self.canvas.before.clear() 
        with self.canvas.before:
            Color(0/255, 155/255, 250/255, 0.75)
            Rectangle(pos = self.pos, size = self.size)

class LightLabel(Label):
    
    def __init__(self, **kwargs):
        Label.__init__(self, **kwargs)
        
        # Formatting the text in the label
        self.bind(size = self.setter("text_size"))
        self.padding = (20, 20)
        self.font_size = 30
        self.halign = "center"
        self.valign = "middle"
        self.color = (1, 1, 1, 1)
        
    def on_size(self, *args):
        # Formatting the colour of the label, by placing a rectangle behind it
        self.canvas.before.clear()
        with self.canvas.before:
            Color(0/255, 155/255, 250/255, 1)
            Rectangle(pos = self.pos, size = self.size)
            
class MyTextInput(TextInput):
    
    def __init__(self, **kwargs):
        TextInput.__init__(self, **kwargs)
        self.font_size = 30
        self.multiline = False
        

### MENU SCREEN ###

class MenuScreen(Screen):
    
    def __init__(self, **kwargs):
        Screen.__init__(self, **kwargs)
        
        root = FloatLayout() # This is the root widget for the screen
        
        title_label = Label(text = "T I M", font_size = 56, color = (1, 1, 1, 1), size_hint = (1, 0.2), pos_hint = {"top": 1})
        root.add_widget(title_label) 
        
        btn1 = DarkButton(text = "Upload Information", on_press = self.upload_information, size_hint = (0.75, 0.13), pos_hint = {"x": 0.12, "y": 0.65})
        root.add_widget(btn1)
        
        btn2 = DarkButton(text = "Get Seating Arrangement", on_press = self.get_seating_arrangement, size_hint = (0.75, 0.13), pos_hint = {"x": 0.12, "y": 0.5})
        root.add_widget(btn2)
        
        btn3 = DarkButton(text = "Get Face List", on_press = self.get_face_list, size_hint = (0.75, 0.13), pos_hint = {"x": 0.12, "y": 0.35} )
        root.add_widget(btn3)
        
        btn4 = DarkButton(text = "Exam Time", on_press = self.exam_time, size_hint = (0.75, 0.13), pos_hint = {"x": 0.12, "y": 0.2})
        root.add_widget(btn4)
        
        btn5 = DarkButton(text = "Quit", on_press = self.quit_app, size_hint = (0.75, 0.13), pos_hint = {"x": 0.12, "y": 0.05})
        root.add_widget(btn5)
        
        self.add_widget(root)
        
    def upload_information(self, instance):
        self.manager.current = "upload information" # This changes the screen to the Upload Information screen
        
    def get_seating_arrangement(self, instance):
        self.manager.current = "get seating arrangement" # This changes the screen to the Get Seating Arrangement screen
        
    def get_face_list(self, instance):
        self.manager.current = "get face list" # This changes the screen to the Get Face List screen
    
    def exam_time(self, instance):
        self.manager.current = "exam time" # This changes the screen to the Exam Time screen
        
    def quit_app(self, instance):
        App.get_running_app().stop() # This quits the app


### UPLOAD INFORMATION SCREEN ###
        
class InfoScreen(Screen):
    
    def __init__(self, **kwargs):
        Screen.__init__(self, **kwargs)
        
        root = FloatLayout() # This is the root widget for the screen
        
        # We split the root widget into 5 different parts:
        
        # First part: sublayout 1
        sublayout1 = GridLayout(cols = 1, size_hint = (0.8, 0.18), pos_hint = {"x": 0.1, "top": 1})
        
        title_label = Label(text = "UPLOAD INFORMATION", halign = "center", valign = "middle", font_size = 48)
        sublayout1.add_widget(title_label)
        
        root.add_widget(sublayout1)
        
        # Second part: sublayout 2
        sublayout2 = GridLayout(cols = 2, size_hint = (0.8, 0.4), pos_hint = {"x": 0.1, "top": 0.82})
        
        lbl1 = LightLabel(text = "Class:", size_hint_x = 0.5)
        sublayout2.add_widget(lbl1)
        
        self.cohort_class = MyTextInput()
        sublayout2.add_widget(self.cohort_class)
        
        lbl2 = DarkLabel(text = "Name:", size_hint_x = 0.5)
        sublayout2.add_widget(lbl2)
        
        self.student_name = MyTextInput()
        sublayout2.add_widget(self.student_name)
        
        lbl3 = LightLabel(text = "Student ID:", size_hint_x = 0.5)
        sublayout2.add_widget(lbl3)
        
        self.student_id = MyTextInput()
        sublayout2.add_widget(self.student_id)
        
        root.add_widget(sublayout2)
        
        # Third part: sublayout 3
        sublayout3 = GridLayout(cols = 1, size_hint = (0.4, 0.1), pos_hint = {"x": 0.3, "top": 0.4})
        
        btn1 = DarkButton(text = "Enter", on_press = self.enter, on_release = self.update_database)
        sublayout3.add_widget(btn1)

        root.add_widget(sublayout3)
        
        # Fourth part: sublayout 4
        sublayout4 = GridLayout(cols = 1, size_hint = (0.8, 0.15), pos_hint = {"x": 0.1, "top": 0.28})
        
        self.output = Label(text = "")
        sublayout4.add_widget(self.output)
            
        root.add_widget(sublayout4)
        
        # Fifth part: sublayout 5
        sublayout5 = GridLayout(cols = 1, size_hint = (0.4, 0.1), pos_hint = {"x": 0.3, "top": 0.11})

        btn2 = DarkButton(text = "Back to Menu", on_press = self.back_to_menu)
        sublayout5.add_widget(btn2)
        
        root.add_widget(sublayout5)
        
        self.add_widget(root)
        
    def enter(self, instance):
        # Used to show that the user has pressed the button
        self.output.text = "Updating..."
        
    def update_database(self, instance):
        # In case user leaves additional spaces when inputting information; important so that Firebase is set up properly
        self.cohort_class.text = self.cohort_class.text.rstrip()
        self.student_id.text = self.student_id.text.rstrip()
        self.student_name.text = self.student_name.text.rstrip()
        
        # Setting the values on the databasel; default for "Attendance" is "Absent"
        db.child("Students' Information").child(self.cohort_class.text).child(self.student_id.text).child("Name").set(self.student_name.text)
        db.child("Students' Information").child(self.cohort_class.text).child(self.student_id.text).child("Attendance").set("Absent")
        
        # Returns the result to user
        self.output.text = "{} ({}) from class {} has been sucessfully updated.".format(self.student_name.text, self.student_id.text, self.cohort_class.text)
        
        # Clear the text input boxes; ready to add next student
        self.student_name.text = ""
        self.student_id.text = ""
    
    def back_to_menu(self, instance):
        self.manager.current = "menu" # This changes the screen to the Menu screen
        
        
### GET SEATING ARRANGEMENT SCREEN ###
        
class SeatScreen(Screen):
    
    def __init__(self, **kwargs):
        Screen.__init__(self, **kwargs)
        
        root = FloatLayout() # This is the root widget for the screen
        
        # We split the root widget into 5 parts:
        
        # First part: sublayout 1
        sublayout1 = GridLayout(cols = 1, size_hint = (0.8, 0.18), pos_hint = {"x": 0.1, "top": 1})
        
        title_label = Label(text = "GET SEATING ARRANGEMENT", halign = "center", valign = "middle", font_size = 48)
        sublayout1.add_widget(title_label)
        
        root.add_widget(sublayout1)
        
        # Second part: sublayout 2
        sublayout2 = GridLayout(cols = 2, size_hint = (0.8, 0.12), pos_hint = {"x": 0.1, "top": 0.82})
        
        lbl1 = LightLabel(text = "Class:", size_hint_x = 0.5)
        sublayout2.add_widget(lbl1)
        
        self.cohort_class = MyTextInput()
        sublayout2.add_widget(self.cohort_class)

        root.add_widget(sublayout2)
        
        # Third part: sublayout 3
        sublayout3 = GridLayout(cols = 3, size_hint = (0.8, 0.08), pos_hint = {"x": 0.1, "top": 0.68})
        
        btn1 = DarkButton(text = "Generate namelist", on_press = self.enter1, on_release = self.generate_namelist)
        sublayout3.add_widget(btn1)
        
        lbl2 = Label(text = "", size_hint_x = 0.1) # We add a black space between the two buttons
        sublayout3.add_widget(lbl2)
        
        self.btn2 = DarkButton(text = "Export as txt", on_press = self.enter2, on_release = self.export)
        sublayout3.add_widget(self.btn2)

        root.add_widget(sublayout3)
        
        # Fourth part: sublayout 4
        sublayout4 = GridLayout(cols = 1, size_hint = (0.8, 0.46), pos_hint = {"x": 0.1, "top": 0.58})
        
        self.scroll = ScrollView()
        sublayout4.add_widget(self.scroll)
                
        root.add_widget(sublayout4)
        
        # Fifth part: sublayout 5
        sublayout5 = GridLayout(cols = 1, size_hint = (0.4, 0.08), pos_hint = {"x": 0.3, "top": 0.1})

        btn3 = DarkButton(text = "Back to Menu", on_press = self.back_to_menu)
        sublayout5.add_widget(btn3)

        root.add_widget(sublayout5)
        
        self.add_widget(root)
        
    def enter1(self, instance):
        # Used to show that the user has pressed the button
        self.scroll.clear_widgets() # Clears all the widgets on the ScrollView
        lbl1 = Label(text = "Loading...")
        self.scroll.add_widget(lbl1)
    
    def generate_namelist(self, instance):
        self.scroll.clear_widgets() # Clears all the widgets on the ScrollView
        self.btn2.text = "Export as txt" # Resets the export button; used when export button has been pressed before and user generates a new namelist, so that the new namelist can be exported again
        
        # In case user leaves additional spaces when inputting information
        self.cohort_class.text = self.cohort_class.text.rstrip()
        
        valid_classes = list(db.child("Students' Information").get().val()) # Obtain a list of the valid classes to check that input is correct

        if self.cohort_class.text not in valid_classes: # Check that the input is correct
            lbl2 = Label(text = "Error: Please enter a valid class.")
            self.scroll.add_widget(lbl2)
            
        else:
            layout = GridLayout(cols = 1, spacing = 10, size_hint_y = None)
            layout.bind(minimum_height = layout.setter("height"))
        
            self.student_id_list = [] # Create a list of the student ID of all students' in a particular class
            for student_id in list(db.child("Students' Information").child(self.cohort_class.text).get().val()):
                self.student_id_list.append(student_id)
            random.shuffle(self.student_id_list) # Randomises the order of the elements in the list to obtain a random seating arrangement
            db.child("Seating Arrangement").child(self.cohort_class.text).set(self.student_id_list) # Upload this random seating arrangement onto Firebase, for easier retrieval in future methods
        
            for student_id in self.student_id_list:
                sn = self.student_id_list.index(student_id) + 1
                if sn < 10:
                    sn = "0" + str(sn)
                else:
                    sn = str(sn)
                student_name = db.child("Students' Information").child(self.cohort_class.text).child(student_id).child("Name").get().val()
                text = "{:<5}{:<10}{}".format(sn, student_id, student_name)
                lbl = Label(text = text, size_hint_y = None, height = 40, text_size = (6.0 * layout.size[0], None))
                layout.add_widget(lbl) 
        
            self.scroll.add_widget(layout)
            
    def enter2(self, instance):
        # Used to show that the user has pressed the button
        self.valid_seating_arrangement = list(db.child("Seating Arrangement").get().val()) # Obtain a list of the valid classes with seating arrangements that have already been generated to check that input is correct
        
        if self.cohort_class.text not in self.valid_seating_arrangement: # Check that the input is correct
            self.scroll.clear_widgets() # Clears all the widgets on the ScrollView
            lbl3 = Label(text = "Error: No seating arrangement found.")
            self.scroll.add_widget(lbl3)
            
        else:
            self.btn2.text = "Exporting..."
        
    def export(self, instance):
        if self.cohort_class.text in self.valid_seating_arrangement:
            f = open("{}.txt".format(self.cohort_class.text), "w")
        
            for i in range(len(self.student_id_list)):
                student_id = self.student_id_list[i] # We use the student id list that was created in generate_namelist to ensure that they match
                student_name = db.child("Students' Information").child(self.cohort_class.text).child(student_id).child("Name").get().val()
                f.write("{:<5}{:<10}{}\n".format(i+1, student_id, student_name))
            
            self.btn2.text = "Exported!" # Used to show that the export is successful

            f.close()
    
    def back_to_menu(self, instance):
        self.manager.current = "menu" # This changes the screen to the Menu screen


### GET FACE LIST SCREEN ###

class FaceScreen(Screen):
    
    def __init__(self, **kwargs):
        Screen.__init__(self, **kwargs)
                
        root = FloatLayout() # This is the root widget for the screen
        
        # We split the root widget into 5 parts:
        
        # First part: sublayout 1
        sublayout1 = GridLayout(cols = 1, size_hint = (0.8, 0.18), pos_hint = {"x": 0.1, "top": 1})
        
        title_label = Label(text = "GET FACE LIST", halign = "center", valign = "middle", font_size = 48)
        sublayout1.add_widget(title_label)
        
        root.add_widget(sublayout1)

        # Second part: sublayout 2
        sublayout2 = GridLayout(cols = 2, size_hint = (0.8, 0.12), pos_hint = {"x": 0.1, "top": 0.82})
        
        lbl1 = LightLabel(text = "Class:", size_hint_x = 0.5)
        sublayout2.add_widget(lbl1)
        
        self.cohort_class = MyTextInput()
        sublayout2.add_widget(self.cohort_class)
        
        root.add_widget(sublayout2)
        
        # Third part: sublayout 3
        sublayout3 = GridLayout(cols = 1, size_hint = (0.4, 0.08), pos_hint = {"x": 0.3, "top": 0.68})
        
        btn1 = DarkButton(text = "Generate Face List", on_press = self.enter, on_release = self.generate_face_list)
        sublayout3.add_widget(btn1)
        
        root.add_widget(sublayout3)
        
        # Fourth part: sublayout 4
        sublayout4 = GridLayout(cols = 1, size_hint = (0.8, 0.46), pos_hint = {"x": 0.1, "top": 0.58})
        
        self.scroll = ScrollView()
        sublayout4.add_widget(self.scroll)
        
        root.add_widget(sublayout4)
        
        # Fifth part: sublayout 5
        sublayout5 = GridLayout(cols = 1, size_hint = (0.4, 0.08), pos_hint = {"x": 0.3, "top": 0.1})
        
        btn2 = LightButton(text = "Back to Menu", on_press = self.back_to_menu)
        sublayout5.add_widget(btn2)

        root.add_widget(sublayout5)
        
        self.add_widget(root)
        
    def enter(self, instance):
        # Used to show that the user has pressed the button
        self.scroll.clear_widgets() # Clears all the widgets on the ScrollView
        lbl1 = Label(text = "Loading...")
        self.scroll.add_widget(lbl1)
        
    def generate_face_list(self, instance):
        self.scroll.clear_widgets() # Clears all the widgets on the ScrollView
        
        # In case user leaves additional spaces when inputting information
        self.cohort_class.text = self.cohort_class.text.rstrip()
        
        valid_classes = list(db.child("Students' Information").get().val()) # Obtain a list of the valid classes to check that input is correct
        valid_seating_arrangement = list(db.child("Seating Arrangement").get().val()) # Obtain a list of the valid classes with seating arrangements that have already been generated to check that input is correct

        if self.cohort_class.text not in valid_classes: # Check that the input is correct
            lbl2 = Label(text = "Error: Please enter a valid class.")
            self.scroll.add_widget(lbl2)
        
        elif self.cohort_class.text not in valid_seating_arrangement: # Check that the input is correct
            lbl2 = Label(text = "Error: No seating arrangement found.")
            self.scroll.add_widget(lbl2)
        else:
            layout = GridLayout(cols = 2, spacing = 10, size_hint_y = None)
            layout.bind(minimum_height = layout.setter("height"))
        
            student_id_list = list(db.child("Seating Arrangement").child(self.cohort_class.text).get().val()) # Obtain the list of student IDs of students in the inputted class, ordered according to the generated seating arrangement
            for i in range(len(student_id_list)):
                sn = i + 1
                student_id = student_id_list[i]
                name = db.child("Students' Information").child(self.cohort_class.text).child(student_id).child("Name").get().val()
                #attendance = db.child("Students' Information").child(self.cohort_class.text).child(student_id).child("Attendance").get().val()
                source = db.child("Students' Information").child(self.cohort_class.text).child(student_id).child("Photo Link").get().val()
                
                text = "{}\n{}\n{}".format(sn, name, student_id)
            
                lbl = Label(text = text, size_hint_y = None, height = 100, font_size = 20, text_size = (2.0 * layout.size[0], None))
                layout.add_widget(lbl)
           
                img = AsyncImage(source = source, size_hint_y = None, height = 100)
                layout.add_widget(img) # Adds in the student's photograph
            
            self.scroll.add_widget(layout)
    
    def back_to_menu(self, instance):
        self.manager.current = "menu" # This changes the screen to the Menu screen
        

### EXAM SCREEN ###
    
class ExamScreen(Screen):
    
    def __init__(self, **kwargs):
        Screen.__init__(self, **kwargs)
        
        root = FloatLayout() # This is the root widget for the screen
        
        # We split the root widget into 6 parts:
        
        # First part: sublayout 1
        sublayout1 = GridLayout(cols = 1, size_hint = (0.8, 0.18), pos_hint = {"x": 0.1, "top": 1})
        
        title_label = Label(text = "EXAM TIME", halign = "center", valign = "middle", font_size = 48)
        sublayout1.add_widget(title_label)
        
        root.add_widget(sublayout1)
        
        # Second part: sublayout 2
        sublayout2 = GridLayout(cols = 2, size_hint = (0.8, 0.13), pos_hint = {"x": 0.1, "top": 0.8})

        lbl1 = LightLabel(text = "Class:", size_hint_x = 0.5)
        sublayout2.add_widget(lbl1)
        
        self.cohort_class = MyTextInput()
        sublayout2.add_widget(self.cohort_class)

        root.add_widget(sublayout2)
        
        # Third part: sublayout 3
        sublayout3 = GridLayout(cols = 5, size_hint = (0.8, 0.08), pos_hint = {"x": 0.1, "top": 0.65})
        
        btn1 = DarkButton(text = "Start Thymio", on_press = self.enter1, on_release = self.start_thymio)
        sublayout3.add_widget(btn1)
        
        lbl2 = Label(text = "", size_hint_x = 0.025) # We add a black space between the button and the label
        sublayout3.add_widget(lbl2)

        self.output = Label(text = "")
        sublayout3.add_widget(self.output)
        
        lbl3 = Label(text = "", size_hint_x = 0.025) # We add a black space between the button and the label
        sublayout3.add_widget(lbl3)
        
        btn2 = DarkButton(text = "Stop Thymio", on_press = self.enter2, on_release = self.stop_thymio)
        sublayout3.add_widget(btn2)

        root.add_widget(sublayout3)
        
        # Fourth part: sublayout 4
        sublayout4 = GridLayout(cols = 3, size_hint = (0.8, 0.08), pos_hint = {"x": 0.1, "top": 0.55})
        
        btn3 = LightButton(text = "Get Attendance List", on_press = self.enter3, on_release = self.get_attendance_list)
        sublayout4.add_widget(btn3)
        
        lbl4 = Label(text = "", size_hint_x = 0.05) # We add a black space between the two buttons
        sublayout4.add_widget(lbl4)
        
        self.btn4 = LightButton(text = "Export as txt", on_press = self.enter4, on_release = self.export)
        sublayout4. add_widget(self.btn4)

        root.add_widget(sublayout4)
        
        # Fifth part: sublayout 5
        sublayout5 = GridLayout(cols = 1, size_hint = (0.8, 0.31), pos_hint = {"x": 0.1, "top": 0.43})
        
        self.scroll = ScrollView()
        sublayout5.add_widget(self.scroll)
                
        root.add_widget(sublayout5)
        
        # Sixth part: sublayout 6
        sublayout6 = GridLayout(cols = 3, size_hint = (0.8, 0.08), pos_hint = {"x": 0.1, "top": 0.1})

        btn5 = DarkButton(text = "Back to Menu", on_press = self.back_to_menu)
        sublayout6.add_widget(btn5)
        
        lbl5 = Label(text = "", size_hint_x = 0.05) # We add a black space between the two buttons
        sublayout6.add_widget(lbl5)

        btn6 = DarkButton(text = "Manual Attendance", on_press = self.manual)
        sublayout6.add_widget(btn6)

        root.add_widget(sublayout6)

        self.add_widget(root)
        
    def enter1(self, instance):
        # Used to show that the user has pressed the button
        
        # In case user leaves additional spaces when inputting information
        self.cohort_class.text = self.cohort_class.text.rstrip()
        
        self.valid_classes = list(db.child("Students' Information").get().val()) # Obtain a list of the valid classes to check that input is correct
        self.valid_seating_arrangement = list(db.child("Seating Arrangement").get().val()) # Obtain a list of the valid classes with seating arrangements that have already been generated to check that input is correct
        
        if self.cohort_class.text not in self.valid_classes: # Check that the input is correct
            self.output.text = "Error: Please enter a valid class."
        elif self.cohort_class.text not in self.valid_seating_arrangement: # Check that the input is correct
            self.output.text = "Error: No seating arrangement found."
        else:
            self.output.text = "Loading..."
    
    def start_thymio(self, instance):
        if self.cohort_class.text in self.valid_classes: # Check that the input is correct
            if self.cohort_class.text in self.valid_seating_arrangement: # Check that the input is correct
                db.child("Movement").child(self.cohort_class.text).set("move")
                self.output.text = "Thymio started"
        
    def enter2(self, instance):
        # Used to show that the user has pressed the button
        self.valid_movements = list(db.child("Movement").get().val()) # Obtain a list of the valid classes that have a thymio running
        if self.cohort_class.text not in self.valid_movements: # Check that the input is correct
            self.output.text = "Error: Thymio not started."
        else:
            self.output.text = "Loading..."
            
    def stop_thymio(self, instance):
        if self.cohort_class.text in self.valid_movements: # Check that the input is correct
            db.child("Movement").child(self.cohort_class.text).set("stop")
            self.output.text = "Thymio stopped"
        
    def enter3(self, instance):
        # Used to show that the user has pressed the button
        self.scroll.clear_widgets() # Clears all the widgets on the ScrollView
        lbl = Label(text = "Loading...")
        self.scroll.add_widget(lbl)
        
    def get_attendance_list(self, instance):
        self.scroll.clear_widgets() # Clears the label that was added when button was pressed
        self.btn4.text = "Export as txt"
        
        # In case user leaves additional spaces when inputting information
        self.cohort_class.text = self.cohort_class.text.rstrip()
        
        # We recreate the list again here for those who want to obtain the attendance list without starting the thymio
        self.valid_classes = list(db.child("Students' Information").get().val()) # Obtain a list of the valid classes to check that input is correct
        self.valid_seating_arrangement = list(db.child("Seating Arrangement").get().val()) # Obtain a list of the valid classes with seating arrangements that have already been generated to check that input is correct
  
        if self.cohort_class.text not in self.valid_classes: # Check that the input is correct
            lbl2 = Label(text = "Error: Please enter a valid class.")
            self.scroll.add_widget(lbl2)
        
        elif self.cohort_class.text not in self.valid_seating_arrangement: # Check that the input is correct
            lbl2 = Label(text = "Error: No seating arrangement found.")
            self.scroll.add_widget(lbl2)
        
        else:
            layout = GridLayout(cols = 2, spacing = 10, size_hint_y = None)
            layout.bind(minimum_height = layout.setter("height"))
        
            self.student_id_list = []
            for student_id in list(db.child("Seating Arrangement").child(self.cohort_class.text).get().val()):
                self.student_id_list.append(student_id)
        
            for student_id in self.student_id_list:
                sn = self.student_id_list.index(student_id) + 1
                if sn < 10:
                    sn = "0" + str(sn)
                else:
                    sn = str(sn)
                student_name = db.child("Students' Information").child(self.cohort_class.text).child(student_id).child("Name").get().val()
                attendance = db.child("Students' Information").child(self.cohort_class.text).child(student_id).child("Attendance").get().val()
                text = "{:<5}{:<10}{}".format(sn, student_id, student_name)
                lbl1 = Label(text = text, size_hint_y = None, height = 40, text_size = (4.5 * layout.size[0], None))
                layout.add_widget(lbl1)
                lbl2 = Label(text = attendance, size_hint_y = None, height = 40, size_hint_x = 0.25)
                layout.add_widget(lbl2)
                
        
            self.scroll.add_widget(layout)
    
    def enter4(self, instance):
        # Used to show that the user has pressed the button
        
        # In case user leaves additional spaces when inputting information
        self.cohort_class.text = self.cohort_class.text.rstrip()
        
        # We recreate the list again here for those who want to export the attendance list without starting the thymio or viewing it on the app
        self.valid_classes = list(db.child("Students' Information").get().val()) # Obtain a list of the valid classes to check that input is correct
        self.valid_seating_arrangement = list(db.child("Seating Arrangement").get().val()) # Obtain a list of the valid classes with seating arrangements that have already been generated to check that input is correct
  
        if self.cohort_class.text not in self.valid_classes: # Check that the input is correct
            self.scroll.clear_widgets() # Clears all the widgets on the ScrollView
            lbl3 = Label(text = "Error: Please enter a valid class.")
            self.scroll.add_widget(lbl3)
            
        elif self.cohort_class.text not in self.valid_seating_arrangement: # Check that the input is correct
            self.scroll.clear_widgets() # Clears all the widgets on the ScrollView
            lbl3 = Label(text = "Error: No attendance list found.")
            self.scroll.add_widget(lbl3)

        else:
            self.btn4.text = "Exporting..."


    def export(self, instance):
        if self.cohort_class.text in self.valid_seating_arrangement: # Check that the input is correct
            f = open("{} attendance.txt".format(self.cohort_class.text), "w")
    
            for i in range(len(self.student_id_list)):
                student_id = self.student_id_list[i]
                student_name = db.child("Students' Information").child(self.cohort_class.text).child(student_id).child("Name").get().val()
                attendance = db.child("Students' Information").child(self.cohort_class.text).child(student_id).child("Attendance").get().val()
                f.write("{:<5}{:<10}{:<40}{}\n".format(i+1, student_id, student_name, attendance))
            
            self.btn4.text = "Exported!"
        
            f.close()    
    


    def back_to_menu(self, instance):
        self.manager.current = "menu" # This changes the screen to the Menu screen
    
    def manual(self, instance):
        self.manager.current = "manual" #This changes the screen to the Manual Attendance Screen

### MANUAL ATTENDANCE SCREEN ###
class ManualScreen(Screen):
    
    def __init__(self, **kwargs):
        Screen.__init__(self, **kwargs)
        
        root = FloatLayout() # This is the root widget for the screen
        
        # We split the root widget into 6 parts:
        
        # First part: sublayout 1
        sublayout1 = GridLayout(cols = 1, size_hint = (0.8, 0.18), pos_hint = {"x": 0.1, "top": 1})
        
        title_label = Label(text = "MANUAL ATTENDANCE", halign = "center", valign = "middle", font_size = 48)
        sublayout1.add_widget(title_label)
        
        root.add_widget(sublayout1)

        # Second part: sublayout 2
        sublayout2 = GridLayout(cols = 2, size_hint = (0.8, 0.24), pos_hint = {"x": 0.1, "top": 0.82})
        
        lbl1 = LightLabel(text = "Class:", size_hint_x = 0.5)
        sublayout2.add_widget(lbl1)
        
        self.cohort_class = MyTextInput()
        sublayout2.add_widget(self.cohort_class)
        
        lbl3 = LightLabel(text = "Student ID:", size_hint_x = 0.5)
        sublayout2.add_widget(lbl3)
        
        self.student_id = MyTextInput()
        sublayout2.add_widget(self.student_id)
        
        root.add_widget(sublayout2)
        
        # Third part: sublayout 3
        sublayout3 = GridLayout(cols = 5, size_hint = (0.8, 0.08), pos_hint = {"x": 0.1, "top": 0.56})
        
        btn1 = DarkButton(text = "Present", on_press = self.enter, on_release = self.update_attendance_present)
        sublayout3.add_widget(btn1)

        lbl4 = Label(text = "", size_hint_x = 0.1) # We add a black space between the two buttons
        sublayout3.add_widget(lbl4)

        btn2 = DarkButton(text = "Absent", on_press = self.enter, on_release = self.update_attendance_absent)
        sublayout3.add_widget(btn2)

        lbl5 = Label(text = "", size_hint_x = 0.1) # We add a black space between the two buttons
        sublayout3.add_widget(lbl5)

        btn3 = DarkButton(text = "Error", on_press = self.enter, on_release = self.update_attendance_error)
        sublayout3.add_widget(btn3)

        root.add_widget(sublayout3)
        
        # Fourth part: sublayout 4
        sublayout4 = GridLayout(cols = 1, size_hint = (0.8, 0.44), pos_hint = {"x": 0.1, "top": 0.5})
        
        self.output = Label(text = "")
        sublayout4.add_widget(self.output)
            
        root.add_widget(sublayout4)
        
        # Fifth part: sublayout 5
        sublayout5 = GridLayout(cols = 1, size_hint = (0.4, 0.08), pos_hint = {"x": 0.3, "top": 0.1})

        btn4 = DarkButton(text = "Back to Exam", on_press = self.back_to_exam)
        sublayout5.add_widget(btn4)
        
        root.add_widget(sublayout5)
        
        self.add_widget(root)
        
    def enter(self, instance):
        # Used to show that the user has pressed the button
        self.output.text = "Updating..."
        
    def update_attendance_present(self, instance):
        # In case user leaves additional spaces when inputting information; important so that Firebase is set up properly
        self.cohort_class.text = self.cohort_class.text.rstrip()
        self.student_id.text = self.student_id.text.rstrip()
        valid_classes = db.child("Seating Arrangement").get().val()
        student_ids = db.child("Students' Information").child(self.cohort_class.text).get().val()
        
        if self.cohort_class.text not in valid_classes:
            self.output.text = "Error: Please input a valid class."
        
        elif self.student_id.text not in student_ids:
            self.output.text = "Error: Please input a valid student id."
        
        else:
            db.child("Students' Information").child(self.cohort_class.text).child(self.student_id.text).child("Attendance").set("Present")
        
            # Returns the result to user
            self.output.text = "Attendance for {} is set to 'Present'.".format(self.student_id.text)
       
            # Clear the text input boxes; ready to add next student
            self.student_id.text = ""
    
    def update_attendance_absent(self, instance):
        # In case user leaves additional spaces when inputting information; important so that Firebase is set up properly
        self.cohort_class.text = self.cohort_class.text.rstrip()
        self.student_id.text = self.student_id.text.rstrip()
        valid_classes = db.child("Seating Arrangement").get().val()
        student_ids = db.child("Students' Information").child(self.cohort_class.text).get().val()
        
        if self.cohort_class.text not in valid_classes:
            self.output.text = "Error: Please input a valid class."        
        
        elif self.student_id.text not in student_ids:
            self.output.text = "Error: Please input a valid student id."
        
        else: 
            db.child("Students' Information").child(self.cohort_class.text).child(self.student_id.text).child("Attendance").set("Absent")
        
            # Returns the result to user
            self.output.text = "Attendance for {} is set to 'Absent'.".format(self.student_id.text)
       
            # Clear the text input boxes; ready to add next student
            self.student_id.text = ""
        
    def update_attendance_error(self, instance):
        # In case user leaves additional spaces when inputting information; important so that Firebase is set up properly
        self.cohort_class.text = self.cohort_class.text.rstrip()
        self.student_id.text = self.student_id.text.rstrip()
        valid_classes = db.child("Seating Arrangement").get().val()
        student_ids = db.child("Students' Information").child(self.cohort_class.text).get().val()
        
        if self.cohort_class.text not in valid_classes:
            self.output.text = "Error: Please input a valid class."
        
        elif self.student_id.text not in student_ids:
            self.output.text = "Error: Please input a valid student id."
        
        else: 
            db.child("Students' Information").child(self.cohort_class.text).child(self.student_id.text).child("Attendance").set("Error")
        
            # Returns the result to user
            self.output.text = "Attendance for {} is set to 'Error'.".format(self.student_id.text)
            
            # Clear the text input boxes; ready to add next student
            self.student_id.text = ""
        
    def back_to_exam(self, instance):
        self.manager.current = "exam time" # This changes the screen to the Menu screen

### ADDING THE SCREENS INTO THE APP ###

class TIM_by_design(App):
    
    def build(self):
        sm = ScreenManager()
        
        # We create 5 different screens:
        
        menu = MenuScreen(name = "menu")
        sm.add_widget(menu)
        
        info = InfoScreen(name = "upload information")
        sm.add_widget(info)
        
        seat = SeatScreen(name = "get seating arrangement")
        sm.add_widget(seat)
        
        face = FaceScreen(name = "get face list")
        sm.add_widget(face)
        
        exam = ExamScreen(name = "exam time")
        sm.add_widget(exam)
        
        manual = ManualScreen(name = "manual")
        sm.add_widget(manual)
        
        sm.current = "menu" # We set the default screen to the Menu screen
        return sm
  

### RUNNING THE APP ###
 
if __name__ == "__main__":
    TIM_by_design().run()

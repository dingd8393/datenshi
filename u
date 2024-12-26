from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.gridlayout import GridLayout
from kivy.uix.label import Label
from kivy.uix.textinput import TextInput
from kivy.uix.spinner import Spinner
from kivy.uix.button import Button
from kivy.uix.screenmanager import ScreenManager, Screen
from kivy.clock import Clock
from datetime import datetime
import pytz  #tpi
import json
import os

# se
SETTINGS_FILE = "schedule.json"

# sn
DEFAULT_SCHEDULE = [{"subject": "自習", "start": "08:00", "end": "09:00"} for _ in range(9)]

class ScheduleScreen(Screen):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.layout = BoxLayout(orientation="vertical")
        self.schedule_data = DEFAULT_SCHEDULE

        # s
        self.load_settings()

        # tb
        self.table = GridLayout(cols=3, row_default_height=50, size_hint_y=None)
        self.table.bind(minimum_height=self.table.setter("height"))

        self.rows = []
        for i in range(9):
            subject_spinner = Spinner(
                text=self.schedule_data[i]["subject"],
                values=["數A", "數B", "社會", "自然", "英文", "國文", "國寫", "自習", "午休"]
            )
            start_input = TextInput(
                text=self.schedule_data[i]["start"],
                multiline=False
            )
            end_input = TextInput(
                text=self.schedule_data[i]["end"],
                multiline=False
            )
            self.table.add_widget(subject_spinner)
            self.table.add_widget(start_input)
            self.table.add_widget(end_input)
            self.rows.append((subject_spinner, start_input, end_input))

        self.layout.add_widget(self.table)

        # bt
        save_button = Button(text="送出", size_hint_y=None, height=50)
        save_button.bind(on_press=self.save_schedule)
        self.layout.add_widget(save_button)
        self.add_widget(self.layout)

    def save_schedule(self, instance):
        # se
        self.schedule_data = []
        for subject, start, end in self.rows:
            self.schedule_data.append({
                "subject": subject.text,
                "start": start.text,
                "end": end.text
            })
        with open(SETTINGS_FILE, "w") as f:
            json.dump(self.schedule_data, f)
        App.get_running_app().screen_manager.current = "clock"

    def load_settings(self):
        # se
        if os.path.exists(SETTINGS_FILE):
            with open(SETTINGS_FILE, "r") as f:
                self.schedule_data = json.load(f)

class ClockScreen(Screen):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.layout = BoxLayout(orientation="vertical")

        # c.
        self.time_label = Label(font_size=72, bold=True, color=[1, 1, 1, 1])
        self.subject_label = Label(font_size=36, color=[1, 1, 1, 1])
        self.layout.add_widget(self.time_label)
        self.layout.add_widget(self.subject_label)

        # bt
        settings_button = Button(text="設定", size_hint_y=None, height=50)
        settings_button.bind(on_press=self.goto_settings)
        self.layout.add_widget(settings_button)

        self.add_widget(self.layout)

        #re
        Clock.schedule_interval(self.update_time, 1)

    def update_time(self, dt):
        # cl t
        taipei_tz = pytz.timezone("Asia/Taipei")
        current_time = datetime.now(taipei_tz).strftime("%H:%M")
        self.time_label.text = current_time

        # sb
        current_subject = "自習"
        if os.path.exists(SETTINGS_FILE):
            with open(SETTINGS_FILE, "r") as f:
                schedule = json.load(f)
            for entry in schedule:
                if entry["start"] <= current_time < entry["end"]:
                    current_subject = entry["subject"]
                    break
        self.subject_label.text = f"現在科目：{current_subject}"

    def goto_settings(self, instance):
        App.get_running_app().screen_manager.current = "schedule"

class MainApp(App):
    def build(self):
        # Scr
        self.screen_manager = ScreenManager()

        self.schedule_screen = ScheduleScreen(name="schedule")
        self.clock_screen = ClockScreen(name="clock")

        self.screen_manager.add_widget(self.schedule_screen)
        self.screen_manager.add_widget(self.clock_screen)

        return self.screen_manager

if __name__ == "__main__":
    MainApp().run()


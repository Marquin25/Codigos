> # Codigos 🪀

## Chatb.py

from kivy.clock import Clock
from kivy.uix.screenmanager import ScreenManager
from kivy.core.window import Window
from kivymd.app import MDApp
from kivy.lang import Builder
from kivymd.uix.label import MDLabel
from kivy.properties import StringProperty, NumericProperty, DictProperty
from kivy.uix.image import Image
from kivy.core.text import LabelBase
import pytube
import os
import wikipedia
from chatterbot import ChatBot
from chatterbot.trainers import ListTrainer
import sqlite3

Window.size = (350,550)
wikipedia.set_lang('pt')

class Command(MDLabel):
    text = StringProperty()
    size_hint_x = NumericProperty()
    haling = StringProperty()
    font_name = 'C:/Windows/Fonts/ARIAL.TTF'
    font_size = 17

class Response(MDLabel):
    text = StringProperty()
    size_hint_x = NumericProperty()
    haling = StringProperty()
    font_name = 'C:/Windows/Fonts/ARIAL.TTF'
    font_size = 17

class ResponseImage(Image):
    source = StringProperty()

class bots(MDApp):
    image_loaded = False

    def change_screen(self, name):
        screen_manager.current = name
                    
    def build(self):
        global screen_manager
        self.title = "B-12"
        screen_manager = ScreenManager()
        screen_manager.add_widget(Builder.load_file('main.kv'))
        screen_manager.add_widget(Builder.load_file('chats.kv'))
        screen_manager.add_widget(Builder.load_file('yout.kv'))
        return screen_manager

    def set_assets(self ,thumbnail , title):
        self.root.ids.thumbnail.source = thumbnail
        self.root.ids.title.text = title
        
    def get_video(self,stream):
        stream.download()

    def get_video_info(self,url):
        try:
            yt = pytube.YouTube(url)
            #self.set_assets(yt.thumbnail_url,yt.title)
            self.image_loaded = True
            Clock.schedule_once(lambda x: self.get_video(yt.streams.get_highest_resolution()),1)
            print('Começando o download do seu vídeo')
        except:
            print("Erro no link ou no vídeo")
            
    def get_mp3_info(self,url):
        try:
            yt=pytube.YouTube(url)
            #self.set_assets(yt.thumbnail_url,yt.title)
            self.image_loaded = True
            Clock.schedule_once(lambda x: self.get_video(yt.streams.get_audio_only()),1)
            print('Começando o download do seu áudio')
        except:
            print("Erro no link ou no áudio")
        screen_manager.get_screen('yout').mp3_btn.add_widget

    def response(self,*args):
        respons = ''
        if value == "oi" or value == "Oi":
            respons = f"Olá, eu sou seu amigo robô {screen_manager.get_screen('chats').bot_name.text} \n"
            respons += "Em que posso ajudar? "
        elif value == "Como vai você?" or value == "como vai você?" or value == "como vai você":
            respons = "estou bem"
        elif '.jpg' in value or '.png' in value:
            screen_manager.get_screen('chats').chat_list.add_widget(ResponseImage(source=value))
        else: 
            respons = "só um momento..."
            respons += wikipedia.summary(value)
        screen_manager.get_screen('chats').chat_list.add_widget(Response(text=respons, size_hint_x= .75))
            
    def send(self):
        global size, haling, value
        if screen_manager.get_screen('chats').text_input != "":
            value = screen_manager.get_screen('chats').text_input.text
            if len(value) < 6:
                size= .22
                haling= "center"
            elif len(value) < 11:
                size= .32
                haling= "center"
            elif len(value) < 16:
                size= .45
                haling= "center"
            elif len(value) < 21:
                size= .58
                haling= "center"
            elif len(value) < 26:
                size= .71
                haling= "center"
            else:
                size= .77
                haling= "left"
            screen_manager.get_screen('chats').chat_list.add_widget(Command(text=value, size_hint_x= size, haling= haling))
            Clock.schedule_once(self.response, 2)
            screen_manager.get_screen('chats').text_input.text = ""

if __name__ == '__main__':
    bots().run()
    
 ## chats.kv
 
 <Command>
    size_hint_y: None
    pos_hint: {"right": .98}
    height: self.texture_size[1]
    padding: 12,10
    theme_text_color: "Custom"
    text_color: 1,1,1,1
    canvas.before:
        Color:
            rgb: (1, 170/255,23/255,1)
        RoundedRectangle:
            size: self.width, self.height
            pos: self.pos
            radius: [23, 23, 0, 23]
<Response>
    size_hint_y: None
    pos_hint: {"x": .02}
    height: self.texture_size[1]
    padding: 12,10
    canvas.before:
        Color:
            rgb: (1, 1, 1, 1)
        RoundedRectangle:
            size: self.width, self.height
            pos: self.pos
            radius: [23, 23, 23, 0]
<ResponseImage>
    size_hint_x: .7
    size_hint_y: 0.0
    height: 200
    allow_stretch: True 
    pos_hint: {"x": .02}
    canvas.before:
        Color:
            rgb: (1, 1, 1, 1)
        RoundedRectangle:
            size: self.width, self.height
            pos: self.pos
            radius: [23, 23, 23, 0]
MDScreen:
    bot_name: bot_name
    text_input: text_input
    chat_list: chat_list 
    name:"chats"
    MDFloatLayout:
        MDFloatLayout:
            md_bg_color: 245/255,245/255,245/255,1
            size_hint_y: .11
            pos_hint: {"center_y": .95}
            MDLabel:
                id: bot_name
                text: "B-12"
                pos_hint: {'center_x': .8, 'center_y': .5}
                haling: "center"
                font_name: 'C:/Windows/Fonts/ARIAL.TTF'
                font_size: "25sp"
                theme_text_color: "Custom"
                text_color: 53/255, 56/255, 60/255, 1
        ScrollView:
            size_hint_y: .77
            pos_hint: {'x': .0,'y': .116}
            do_scroll_x: False
            do_scroll_y: True
            MDBoxLayout:
                id: chat_list
                orientation:'vertical'
                size: (root.width, root.height)
                height: self.minimum_height
                size_hint: None,None
                pos_hint: {'top': 10}
                cols:1
                spacing:5
                MDProgressBar: 
                    min: 0
                    max: 100
                    id: progress_bar_width
        MDIconButton:
            icon: "download"
            text:'yout'
            pos_hint: {'center_x': .90,'center_y': .20}
            user_font_theme: "18sp"
            theme_text_color: "Custom"
            text_color: 1, 1, 1, 1
            md_bg_color: 1, 170/255, 23/255, 1
            on_release: 
                root.manager.transition.direction = "left"
                root.manager.current = "yout" 
        MDFloatLayout:
            md_bg_color: 245/255,245/255,245/255,1
            size_hint_y: .11
            MDFloatLayout:
                size_hint: .8, .75
                pos_hint: {'center_x': .43,'center_y': .5}
                canvas:
                    Color:
                        rgb: (238/255, 238/255, 238/255, 1)
                    RoundedRectangle:
                        size: self.size
                        pos: self.pos
                        radius: [23, 23, 23, 23]
                TextInput:
                    id: text_input
                    hint_text: "digite..."
                    size_hint: 1, None
                    pos_hint: {'center_x': .5,'center_y': .5}
                    font_size: "18sp"
                    height: self.minimum_height
                    cursor_color:1, 170/255, 23/255, 1
                    cursor_width: "2sp"
                    foreground_color: 1, 170/255, 23/255, 1
                    background_color: 0,0,0,0
                    padding: ('15dp', '15dp', '15dp', '15dp')
                    font_name:"C:/Windows/Fonts/ARIAL.TTF"
            MDIconButton:
                icon: "send"
                pos_hint: {'center_x': .91,'center_y': .5}
                user_font_theme: "18sp"
                theme_text_color: "Custom"
                text_color: 1, 1, 1, 1
                md_bg_color: 1, 170/255, 23/255, 1
                on_release: 
                    app.send()
    
    
   ## youtube.kv
    
    MDScreen:
    txt_input: txt_input
    save_btn: save_btn
    mp3_btn: mp3_btn
    name: 'yout'
    MDFloatLayout:
        canvas.before:
            Color:
                rgba: rgba("#000000")
            Rectangle:
                size: self.size
                pos: self.pos
        MDLabel:
            text: 'B-12'
            size_hint: [None, None]
            size: self.texture_size
            pos_hint: {'center_x': .8, 'center_y': .5}
            font_size: "12sp"
            color:rgba("#FF0000")
            bold: True
            canvas.before:
                Color:
                    rgba: rgba("#000000")
                Rectangle:
                    size: self.size
                    pos: self.pos
        MDLabel:
            text: "você pode baixar vídeos e áudios do youtube"
            size: self.texture_size
            pos_hint: {'center_x': .65,'center_y': .85}
            font_name: 'C:/Windows/Fonts/ARIAL.TTF'
            font_size: "15sp"
            color:rgba("#FF0000")
            bold: True
            canvas.before:
                Color:
                    rgba: rgba("#000000")
                Rectangle:
                    size: self.size
                    pos: self.pos
        TextInput:
            id: txt_input
            background_color: [1,1,1,1]
            pos_hint: {"top":0.8, "center_x":0.5}
            color: [0,0,0,1]
            bold: True
            font_size: "12sp"
            multiline: False
            size_hint: [0.85, 0.06]      
        Button:
            id: save_btn
            text: "Mp4"
            background_normal: ""
            background_color: rgba("#FF0000")
            color: [1,1,1,1]
            bold: True
            font_size: "17sp"
            pos_hint: {"top":0.7, "x":0.1}
            size_hint: [0.3, 0.08]
            on_press:
        Button:
            id: mp3_btn
            text: "Mp3"
            background_normal: ""
            background_color: rgba("#FF0000")
            color: [1,1,1,1]
            bold: True
            font_size: "18sp"
            pos_hint: {"top":0.7, "center_x":0.7}
            size_hint: [0.3, 0.08]
            on_press:
        ProgressBar:
            id: progress_bar
            size_hint: [0.8, 0.1]
            pos_hint: {"top":0.5, "center_x":0.5}
            value: 0
        MDLabel:
            id: title
            text: "título do vídeo".title()
            size_hint: [None, None]
            size: self.texture_size
            pos_hint: {'center_x': .45,'center_y': .55}
            font_name: 'C:/Windows/Fonts/ARIAL.TTF'
            font_size: '15'
            bold: True
            color: rgba("#FF0000")
            canvas.before:
                Color:
                    rgba: rgba("#000000")
                Rectangle:
                    size: self.size
                    pos: self.pos
        AsyncImage:
            id: thumbnail
            background_color: [1,1,1,1]
            pos_hint: {"top":0.4, "center_x":0.5}
            size_hint: [0.9, 0.3]
            source: ''
    
    
    

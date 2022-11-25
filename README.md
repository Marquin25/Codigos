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
    
  ## menu.py
    
    import os
from kivy.app import App
from kivy.clock import Clock
from pytube import YouTube
from kivy.lang import Builder
import pytube
from kivy.clock import Clock

kv = Builder.load_file('main.kv')

class MyApp(App):
    image_loaded = False

    def build(self):
        self.title = "Bot Youtube"
        return kv

    def set_assets(self ,thumbnail , title):
        self.root.ids.thumbnail.source = thumbnail
        self.root.ids.title.text = title

    def get_video(self,stream):
        stream.download()

    def get_video_info(self,url):
        try:
            yt = pytube.YouTube(url)
            self.set_assets(yt.thumbnail_url,yt.title)
            self.image_loaded = True
            Clock.schedule_once(lambda x: self.get_video(yt.streams.get_highest_resolution()),1)
            print('Começando o download do seu vídeo')
        except:
            print("Erro no link ou no vídeo")

    def get_mp3_info(self,url):
        try:
            yt=pytube.YouTube(url)
            self.set_assets(yt.thumbnail_url,yt.title)
            self.image_loaded = True
            Clock.schedule_once(lambda x: self.get_video(yt.streams.get_audio_only()),1)
            print('Começando o download do seu áudio')
        except:
            print(" Erro")

if __name__=="__main__":
    MyApp().run()
    
    

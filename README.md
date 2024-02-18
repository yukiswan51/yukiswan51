#!usr/bin/env python

#imports
import PIL.Image # python-imaging
import PIL.ImageStat # python-imaging
import Xlib.display # python-xlib
import os # Import os
from Xlib import display
from array import array
try:
   import cPickle as pickle
except:
   import pickle
import sys
platform=sys.platform
if(platform=="win32"):
	operating_system=1
elif(platform="darwin"):
	operating_system=3
else:
	operating_system=-1

screen_width=0
screen_height=0
###### Functions
def is_number(s):
    try:
        float(s)
        return True
    except ValueError:
        return False
        
def get_pixel_colour(i_x, i_y):
    o_x_root = Xlib.display.Display().screen().root
    o_x_image = o_x_root.get_image(i_x, i_y, 1, 1, Xlib.X.ZPixmap, 0xffffffff)
    o_pil_image_rgb = PIL.Image.fromstring("RGB", (1, 1), o_x_image.data, "raw", "BGRX")
    lf_colour = PIL.ImageStat.Stat(o_pil_image_rgb).mean
    return map(int, lf_colour)

def output_tap_key(key):
	if(operating_system==1):
		print("No function found!")
	else:
		os.system("/usr/bin/xvkbd -xsendevent -text \"" + key + "\"")

######### Inputs
def process_monitors():
	global screen_width
	global screen_height
	for monitor in total_monitors:
		colour_match_type=int(monitor[0][0])
		print(colour_match_type)
		if(int(monitor[1][0])==1):
			x = screen_width
			y = screen_height
		elif(int(monitor[1][0])==2):
			
			data = display.Display().screen().root.query_pointer()._data
			y = data["root_y"]
			x = data["root_x"]
		elif(int(monitor[1][0])==4):
			x=int(monitor[1][1][0])
			y=int(monitor[1][1][1])
		
		# The the colour of the pixel we want
		pixel_data=get_pixel_colour(x,y)
		pixel_red=pixel_data[0]
		pixel_green=pixel_data[1]
		pixel_blue=pixel_data[2]
		
		if(colour_match_type==1):
			result=process_colour_exact(pixel_red,pixel_green,pixel_red,monitor[0][1][0],monitor[0][1][1],monitor[0][1][2])
		elif(colour_match_type==2):
			result=process_colour_between(monitor[0][1][1][0], monitor[0][1][1][1], monitor[0][1][1][2], monitor[0][1][0][0], monitor[0][1][0][1], monitor[0][1][0][2], pixel_red, pixel_green, pixel_red)
		print(monitor[2][1])
		if(result):
			if(monitor[2][0]==1):
				print("We got a hit and you want us to do NOTHING?")
			elif(monitor[2][0]==2):
				print("We Are going to push the key: " + monitor[2][1])
				output_tap_key(monitor[2][1])
			elif(monitor[2][0]==3):
				print("We are going to push the mouse button.")
				print("But we dont no how :(")
			elif(monitor[2][0]==4):
				print("Excuting Command: " + monitor[2][1])
				os.system(monitor[2][1])
	
def process_colour_exact(in_red, in_green, in_blue, out_red, out_green, out_blue):
	if(in_red==out_red and in_blue==out_blue and in_green == out_green):
		return True
	else:
		return False
		
def process_colour_between(h_red,h_blue,h_green,l_red,l_blue,l_green,a_red,a_blue,a_green):
	if((h_red + 1 > a_red > l_red - 1) and (h_blue + 1 > a_blue > l_blue - 1) and (h_green+1 >a_green > l_green-1)):
		return True
	else:
		return False

def input_add_white_space():
	x=0
	while(x < 100):
		print("")
		x=x+1
		
# input for exact matching colours
def input_exact_match():
	print("Now we need to set up the colours; ")
	c_red=raw_input(" What is the value of Red? ")
	c_green=raw_input(" What is the value of Green? ")
	c_blue=raw_input(" What is the value of Blue? ")
	returnme=[c_red,c_green,c_blue]
	input_add_white_space()
	return returnme

# input for between to colours
def input_difference():
	print("Now we need to set up the colours; ")
	
	do_while=True
	while(do_while):
		cl_red=raw_input(" What is the lowest value of Red? ")
		if(is_number(cl_red) and len(cl_red)>0):	
			if((int(cl_red) > 0) and (int(cl_red) < 255)):
				do_while=False
			else:
				do_while=True
		else:
			print("number wonra")
	
	do_while=True
	while(do_while == True):
		cl_green=raw_input(" What is the lowest value of Green? ")
		if(is_number(cl_green) and len(cl_green)>0):
			if((int(cl_green) < 0) or (int(cl_green) > 255)):
				do_while=True
			else:
				do_while=False
			
	do_while=True
	while(do_while == True):
		cl_blue=raw_input(" What is the lowest value of Blue? ")
		if(is_number(cl_blue) and len(cl_blue)>0):	
			if((int(cl_blue) < 0) or (int(cl_blue) > 255)):
				do_while=True
			else:
				do_while=False
	
	do_while=True
	while(do_while == True):
		ch_red=raw_input(" What is the highest value of Red? ")
		if(is_number(ch_red) and len(ch_red)>0):
			if((int(ch_red) < 0) or (int(ch_red) > 255)):
				do_while=True
			else:
				do_while=False
	
	do_while=True
	while(do_while == True):
		ch_green=raw_input(" What is the highest value of Green? ")
		if(is_number(ch_green) and len(ch_green)>0):
			if((int(ch_green) < 0) or (int(ch_green) > 255)):
				do_while=True
			else:
				do_while=False
	
	do_while=True
	while(do_while == True):
		ch_blue=raw_input(" What is the highest value of Blue? ")
		if(is_number(ch_blue) and len(ch_blue)>0):
			if((int(ch_blue) < 0) or (int(ch_blue) > 255)):
				do_while=True
			else:
				do_while=False
	
	returnme=[[int(cl_red), int(cl_green), int(cl_blue)],[int(ch_red), int(ch_green), int(ch_blue)]]
	input_add_white_space()
	return returnme


def input_where():
	print("Where do you want us to monitor?")
	print(" 1) Center of Screen")
	print(" 2) Cursor Location")
	print(" 3) Full Screen - Warning: This may slow down your PC.")
	print(" 4) Set Location")
	do_while=True
	while(do_while):
		tanswer=raw_input("Your Answer: ")
		if(is_number(tanswer)):
			tanswer=int(tanswer)
			if(tanswer==1 or tanswer==2 or tanswer==3):
				do_while=False
				input_add_white_space()
				return [int(tanswer)]
			elif(tanswer==4):
				do_while=False
				do_while2=True
				input_add_white_space()
				while(do_while2):
					print("What is the location of the pixel you want me to match?")
					do_while3=True
					while(do_while3):
						x=raw_input(" What is the value of x? ")
						if(is_number(x)):
							do_while3=False
					do_while3=True
					while(do_while3):
						y=raw_input(" What is the value of y? ")
						if(is_number(x)):
							do_while3=False
					return [int(tanswer),[int(x),int(y)]]
	
def input_colour_what():
	print("What am I looking for?")
	print(" 1) Set colour")
	print(" 2) Colour between two colours")
	do_while=True
	while(do_while):
		tanswer=raw_input("Your Answer: ")
		if(is_number(tanswer)):
			if((int(tanswer) == 1)  or (int(tanswer) == 2)):
				do_while=False
	tanswer=int(tanswer)
	if(tanswer==1):
		r = input_exact_match()
	else:
		r = input_difference()
	input_add_white_space()
	return [tanswer, r]
	
def input_do_what_push_key():
	print("What key do you want me to push when I get a match?")
	do_while=True
	while(do_while):
		eanswer = raw_input("Your Answer: ")
		if(len(eanswer)==1):
			do_while=False
	return eanswer
	
def input_do_what_push_mouse():
	print("What mouse button do you want me to push?")
	print(" 1) Left Button")
	print(" 2) Right Button")
	do_while=True
	while(do_while):
		eanswer = raw_input("Your Answer: ")
		if(is_number(eanswer)):
			if(int(eanswer)==1 or int(eanswer)==2):
				do_while=False
	return eanswer
	
def input_do_what():
	print("What do you want me to do when I get a match?")
	print(" 1) Nothing")
	print(" 2) Push a key")
	print(" 3) Push a mouse button")
	print(" 4) Execute Command")
	do_while=True
	while(do_while):
		what_answer=raw_input("Your Answer: ")
		if(is_number(what_answer)):
			if((int(what_answer) > 0)  and (int(what_answer) < 5)):
				do_while=False
				what_answer=int(what_answer)
	input_add_white_space() #needs more info
	action_answer=0
	if(what_answer==1):
		action_answer=0
	elif(what_answer==2):
		action_answer=input_do_what_push_key()
	elif(what_answer==3):
		action_answer=input_do_what_push_mouse()
	
	returnme = [what_answer, action_answer]
	return returnme

def input_create_new_monitor():
	where = input_where()
	colour = int(input_colour_what())
	if(colour == 1):
		match = input_exact_match()
	else:
		match = input_difference()
	colour_result=[colour,match]
	what = input_do_what()
	input_add_white_space()
	return [where,colour_result,what]
# End of menus



def input_monitors_save(the_thread):
	file_location=raw_input("What is the name of the file you which to save? ")
	output = open(file_location, 'wb')
	# Pickle dictionary using protocol 0.
	pickle.dump(the_thread, output)
	output.close()

def input_monitors_load():
	global total_monitors
	file_location=raw_input("What is the name of the file you which to open? ")
	pkl_file = open(file_location, 'rb')
	total_monitors = pickle.load(pkl_file)
	pkl_file.close()
	print(total_monitors[0][0])
	total_monitors.remove([total_monitors[0][0]])


def input_monitors_start():
	print("Do you want to start the AimBot?")
	print(" 1) Yes")
	print(" 2) No")
	do_while=True
	while(do_while):
		what_answer=raw_input("Your Answer: ")
		if(is_number(what_answer)):
			what_answer=int(what_answer)
			if(what_answer==1):
				do_while=False
				pre_process_monitors()
				while True:
					process_monitors()
			elif(what_answer==2):
				do_while=False
	input_add_white_space()

def pre_process_monitors():
	global screen_width
	global screen_height
	
	if(operating_system==1):
		from win32api import GetSystemMetrics
		screen_width = GetSystemMetrics (0)
		screen_height = GetSystemMetrics (1)
	else:
		do_while3=True
		while(do_while3):
			screen_width=raw_input("What is your screen resolutions width? ")
			if(is_number(screen_width)):
				do_while3=False
		
		do_while3=True
		while(do_while3):
			screen_height=raw_input("What is your screen resolutions height? ")
			if(is_number(screen_height)):
				do_while3=False
		screen_height = int(round(int(screen_height) // 2))
		screen_width  = int(round(int(screen_width) // 2))

def input_main_menu():
	print("   ____              _   _     ____  U _____ e   ____     ____    ")
	print("U / ___|u   ___     | \ |-| U / ___|g\| ___ |/U |  _ \ uR|  _ \ u ")
	print("\| |  _ /  |_-_|   <|  \| |>\| |  _ / |  _|    \| |_) |/\| |_) |/ ")
	print(" | |_| |    | |    i| |\  |n | |_| |  | |___    |  _ <   |  __/   ")
	print("  \____|  U/| |\G   |_| \_|   \____|  |_____|   |_| \_\  |_|      ")
	print("  _)(|_.-,_|___|_,-.||   \\,-._)(|_   <<   >>   //   \\_ ||>>_    ")
	print(" (__)__)\_)-' '-(_/ (_ )  (_/(__)__) (__) (__) (__)  (__)__)__)   ")
	print("")
	print("")
	print("    __    ____  __  __  ____  _____  ____    __  __    __    _  _  ____  ____  ")
	print("   /__\  (_  _)(  \/  )(  _ \(  _  )(_  _)  (  \/  )  /__\  ( )/ )( ___)(  _ \ ")
	print("  /(__)\  _)(_  )    (  ) _ < )(_)(   )(     )    (  /(__)\  )  (  )__)  )   / ")
	print(" (__)(__)(____)(_/\/\_)(____/(_____) (__)   (_/\/\_)(__)(__)(_)\_)(____)(_)\_) ")

	print("")
	print("")
	print("")
	print("")
	print("")
	print("")

	print("What do you want to do?")
	print(" 1) Load a previous file")
	print(" 2) Create a new configuration")
	print(" 3) Exit")
	do_while=True
	while(do_while):
		eanswer = raw_input("Your Answer: ")
		if(is_number(eanswer)):
			if(int(eanswer)==1 or int(eanswer)==2 or int(eanswer)==3):
				do_while=False
				eanswer=int(eanswer)
		input_add_white_space()
	if(eanswer==1):
		input_monitors_load()
		input_monitors_start()
	elif(eanswer==2):
		do_while=True
		total_monitors=[["Made With Version 1"]]
		while(do_while):
			total_monitors[len(total_monitors):]=[[input_colour_what(),input_where(),input_do_what()]]
			input_add_white_space()
			print("Do you want to create another monitor? ")
			print(" 1) Yes")
			print(" 2) No")
			do_while2=True
			while(do_while2):
				g=raw_input("Your answer: ")
				if(is_number(g)):
					g=int(g)
					if(g==2):
						do_while2=False
						do_while=False
					elif(g==1):
						do_while2=False
		do_while=True
		print("Do you want to save this configuration? ")
		print(" 1) Yes")
		print(" 2) No")
		while(do_while):
			o=raw_input("Your Answer: ")
			if(is_number(o)):
				o=int(o)
				if(o==1):
					input_monitors_save(total_monitors)
					do_while=False
				elif(o==2):
					do_while=False
		
	elif(eanswer==3):
		sys.exit(1)
#Start the program

input_add_white_space()
total_monitors=list

while True:
	input_main_menu()

raw_input("Thats it all done. Press enter to quit")
sys.exit(1)

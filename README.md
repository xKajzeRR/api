📓Events
List of cheat events that you can listen to using :set

List of game events:
In order to reference an event, index the events namespace with an event name

Logo
Counter-Strike: Global Offensive Events - AlliedModders Wiki
Official CS:GO events
Copy
local hitgroup_str = {
    [0] = 'generic',
    'head', 'chest', 'stomach',
    'left arm', 'right arm',
    'left leg', 'right leg',
    'neck', 'generic', 'gear'
}

events.player_hurt:set(function(e)
    local me = entity.get_local_player()
    local attacker = entity.get(e.attacker, true)

    if me == attacker then
        local user = entity.get(e.userid, true)
        local hitgroup = hitgroup_str[e.hitgroup]

        print(('Hit %s in the %s for %d damage (%d health remaining)'):format(
            user:get_name(), hitgroup,
            e.dmg_health, e.health
        ))
    end
end)
List of events:
render
Fired every frame. Most functions from the render namespace can only be used here.

Copy
events.render:set(function(ctx)
    render.rect(vector(0, 0), vector(1920, 1080), color(230, 150))
end)
render_glow
Fired every time the game prepares glow object manager. This event gives you the ability to render glow lines. Access the function by adding an argument to the callback.

ctx:render(from: vector, to: vector, thickness: number, flags: string, color: color)

Name	Type	Description
from

vector

Start position in world space

to

vector

Final position in world space

thickness

number

Line thickness as a number in the range [0.0, ∞]

flags

string

Glow flags. 
l
 to draw line, 
g
 to draw glow outline, or 
w
 to make it fully visible behind walls

color

color

Color of the line

override_view
Fired every time the game prepares camera view.

Name	Type	Description
fov

number

Field of View

view

vector

Camera view angles

camera

vector

World position of the camera

createmove
Fired every time the game prepares a move command. Use this to modify something before the aimbot or movement features. Use the parameter passed by the callback to access the UserCmd.

Copy
-- Sets the Roll angle when Shift (+speed) is being held
events.createmove:set(function(cmd)
    if cmd.in_speed then
        cmd.view_angles.z = 50 -- you are now unhittable
    end
end)
🔗 struct 
UserCmd
Name	Type	Description
block_movement

number

Set to 
1
 to make the cheat slowdown you to the weapon's minimal speed or set to 
2
 to fully stop you. Defaults to 
0

no_choke

boolean

Set to 
true
 to force the cheat to not choke the current command

send_packet

boolean

Set to 
false
 to force the cheat to choke the current command

force_defensive

boolean

Set to 
true
 to trigger 
'defensive'
 exploit (Double tap is required to be fully charged)

jitter_move

boolean

Set to 
false
 to disable jitter move

choked_commands

number

Amount of choked commands

command_number

number

Current command number

tickcount

number

Current command tickcount

random_seed

number

Current command random seed

view_angles

vector

Player view angles

move_yaw

number

Movement yaw angle

forwardmove

number

Forward / backward speed

sidemove

number

Left / right speed

upmove

number

Up / down speed

createmove_run
Fired every time the game runs a move command. Use the parameter passed by the callback to access the RunCommand.

🔗 struct 
RunCommand
Name	Type	Description
choked_commands

number

Amount of choked commands

command_number

number

Current command number

tick_count

number

Current command tick count

move_yaw

number

Movement yaw angle

forwardmove

number

Forward / backward speed

sidemove

number

Left / right speed

upmove

number

Up / down speed

aim_fire
Fired every time the aimbot shoots at a player.

Name	Type	Description
id

number

Shot ID

target

entity

Target entity

damage

number

Estimated damage

hitchance

number

Estimated hit chance

hitgroup

number

Targeted hitgroup

backtrack

number

Amount of ticks the player was backtracked

aim

vector

World position of the aim point

angle

vector

Aimbot shoot angles

aim_ack
Name	Type	Description
id

number

Shot ID

target

entity

Target entity

damage

number

Actual shot damage

spread

number

Bullet spread angle if available

hitchance

number

Actual shot hit chance

hitgroup

number

Hitgroup that was hit

backtrack

number

Amount of ticks the player was backtracked

aim

vector

World position of the aim point

wanted_damage

number

Targeted damage

wanted_hitgroup

number

Targeted hitgroup

state

string

Reason the shot was missed or nil if the shot was hit. Available miss reasons: spread, correction, misprediction, prediction error, backtrack failure, damage rejection, unregistered shot, player death, death.

bullet_fire
Fired every time someone fires a bullet.

Name	Type	Description
entity

entity

Entity that did the shot

origin

vector

Entity world position

angles

vector

Aim angle based on entity rotation

sound

number

Sound type

spread

number

Weapon spread

inaccuracy

number

Weapon inaccuracy

recoil_index

number

Weapon recoil index

random_seed

number

Spread seed of the shot

weapon_id

number

Weapon definition index

weapon_mode

number

Weapon fire mode

console_input
Fired every time the user runs a console command. Use the parameter passed by the callback to access the input string.

Copy
local last_random_int

events.console_input:set(function(text)
    if text == '/roll' then
        local random_int = utils.random_int(1, 6)

        local str = common.get_username() .. ' rolled a ' .. random_int
        if random_int == 1 and random_int == last_random_int then
            str = str .. '... snake eyes!'
        end

        print(str)

        last_random_int = random_int
        return false
    end
end)
This can be used to implement custom console commands. Return 
false
 to prevent the game from processing the command.

draw_model
Fired before a model is rendered. Use the parameter passed by the callback to access the model context. Return 
false
 to prevent the game from rendering the original model.

Name	Type	Description
name

string

Name of the model. (e.g: weapons\v_knife_cord.mdl)

entity

entity

Entity that belongs to the model.

draw

function

Draws the model with the specified material. Pass nil to the first argument to draw the model with the default material.

level_init
Fired after fully connected to the server (first non-delta packet received). (SIGNONSTATE:FULL)

pre_render
Fired before a frame is rendered. (FrameStageNotify:FRAME_RENDER_START)

post_render
Fired after a frame is rendered. (FrameStageNotify:FRAME_RENDER_END)

net_update_start
Fired before the game processes entity updates from the server. (FrameStageNotify:FRAME_NET_UPDATE_START)

net_update_end
Fired after an entity update packet is received from the server. (FrameStageNotify:FRAME_NET_UPDATE_END)

config_state
Fired every time config state is updated. The current state is accessible from the callback arguments as one of these strings: 
pre_save
, 
post_save
, 
pre_load
, 
post_load
.

Copy
events.config_state(function(state)
    print(state == "pre_save")
end)
mouse_input
Fired every time the mouse input occurs. Return 
false
 to lock the mouse input.

shutdown
Fired when the script is about to unload.

pre_update_clientside_animation
Fired before C_CSPlayer::UpdateClientSideAnimation is called.

Name	Type	Description
player

entity

...

post_update_clientside_animation
Fired after C_CSPlayer::UpdateClientSideAnimation is called.

Name	Type	Description
player

entity

...

grenade_override_view
Invoked to override the input values for the grenade prediction. Contains detailed view parameters associated with the grenade trajectory prediction.

Name	Type	Description
angles

vector

Input view angles

src

vector

Input starting position or origin

velocity

vector

Input velocity

view_offset

vector

Input view offset

grenade_warning
Fired when the "Grenade Proximity Warning" is being rendered. Return false to it from being rendered.

Name	Type	Description
entity

entity

The game entity representing the grenade in proximity.

origin

vector

The current position of the grenade.

closest_point

vector

Represents the nearest point to the player where the grenade will cause damage. For example, in the case of a molotov, this point indicates where the flames would be most harmful.

type

string

Specifies the type of the grenade, "Frag" or "Molly".

damage

number

Predicts the potential damage that would be inflicted upon the local player if they remain at their current position when the grenade detonates.

expire_time

number

Specifies the time when the grenade detonates or is no longer a threat.

icon

ImgObject

A reference to the texture used in the warning.

path

table

Table of 3D vectors representing the complete trajectory path of the grenade.

grenade_prediction
Fired when the cheat is drawing the predicted grenade trajectory. Contains detailed information about the grenade's trajectory and impact.

Name	Type	Description
type

string

Identifies the type of the grenade, e.g., "Smoke", "Flash", "Frag".

damage

number

Represents the amount of damage inflicted upon the target due to the grenade's effect.

fatal

boolean

Indicates whether the grenade's effect resulted in a lethal outcome for the target.

path

table

Table of 3D vectors representing the complete trajectory path of the grenade.

collisions

table

Table of 3D vectors containing all the collision points where the grenade interacts with an obstacle or wall.

target

entity

The game entity that the grenade directly impacts or affects.

localplayer_transparency
Invoked to override the opacity of the local player's model. You can override it by returning a custom alpha value.

Name	Type	Description
current_alpha

number

The current alpha. Ranges from 0 (completely transparent) to 255 (completely opaque).

List of complicated events:
voice_message
Fired every time the game receives a voice packet.

Name	Type	Description
entity

entity

Entity that belongs to the voice packet.

audible_mask

number

Audible mask

xuid

number

Xuid

proximity

number

Proximity

format

number

Format

sequence_bytes

number

Sequence bytes

section_number

number

Section number

uncompressed_sample_offset

number

Uncompressed sample offset

buffer

bf_read

Voice packet buffer

is_nl

boolean

Packet was sent by the Neverlose

🔗 struct 
bf_read
Name	Type	Description
read_bits

function

Reads a number value from the buffer :read_bits(num_bits)

read_coord

function

Reads a floating number value from the buffer :read_coord() (4 bytes)

reset

function

Resets the pointer of the buffer to its original offset

crypt

function

Encrypts/decrypts buffer :crypt(key)

🔗 struct 
bf_write
Name	Type	Description
write_bits

function

Writes a number value to the buffer :write_bits(value, num_bits)

write_coord

function

Writes a floating number value to the buffer :write_coord(value) (4 bytes)

is_overflowed

function

Returns 
true
 if the buffer is overflowed

crypt

function

Encrypts/decrypts buffer :crypt(key)

📌 Firing this event from the Lua will send a voice packet

events.voice_message(function: buffer)

Copy
events.voice_message(function(ctx)
    local buffer = ctx.buffer
    local code = buffer:read_bits(16)

    if code ~= 0x1337 then
        return
    end

    local tickcount = buffer:read_bits(32)

    print(string.format(
        'received voice packet from %s | pct_tickcount: %d',
        ctx.entity:get_name(), tickcount
    ))
end)

-- Note that you wont be able to receive your own voice packet
-- unless voice_loopback convar is set to 1
events.voice_message:call(function(buffer)
    buffer:write_bits(0x1337, 16)
    buffer:write_bits(globals.tickcount, 32)
end)

🌎_G
assert
assert(expression: any[, text: string, ...]): any

Name	Type	Description
expression

any

The expression to assert.

text

text

The error message to throw when assertion fails. This is only type-checked if the assertion fails.

...

any

Any arguments past the error message will be returned by a successful assert.

If the result of the first argument is false or nil, an error is thrown with the second argument as the message.

error
error(text: any[, level: number])

Name	Type	Description
text

string

The error message to throw.

level

number

The level to throw the error at.

Throws a Lua error and breaks out of the current call stack.

getmetatable
getmetatable(object: any): any

Name	Type	Description
object

any

The value to return the metatable of.

Returns the metatable of an object. This function obeys the metatable's __metatable field, and will return that field if the metatable has it set.

ipairs
ipairs(tbl: table): function, table, number

Name	Type	Description
tbl

table

The table to iterate over.

Returns an iterator function for a for loop, to return ordered key-value pairs from a table.

print
print(text: string[, ...])

Name	Type	Description
text

string

Text to print into the console.

...

any

Optional arguments to concatenate with text.

Prints the text to the console.

print_error
print_error(text: string[, ...])

Name	Type	Description
text

string

Text to print into the console.

...

any

Optional arguments to concatenate with text.

Prints an error to the console and plays a sound.

print_chat
print_chat(text: string[, ...])

Name	Type	Description
text

string

Text to print into the in-game chat.

...

any

Optional arguments to concatenate with text.

Prints the text to the in-game chat.

print_raw
print_raw(text: string[, ...])

Name	Type	Description
text

string

Text to print into the console.

...

any

Optional arguments to concatenate with text.

Prints the text that can be changed in color by prepending it with "\a" followed by the color in the hexadecimal "RRGGBB" format. For example, "\aFF0000Hi" will print "Hi" in red.

print_dev
print_dev(text: string[, ...])

Name	Type	Description
text

string

Text to print to into the upper-left console panel.

...

any

Optional arguments to concatenate with text.

Prints the text into the upper-left console panel.

tonumber
tonumber(value: any[, base: number]): 
number

Name	Type	Description
value

any

The value to convert. Can be a number or string.

base

number

Optional. The base used in the string. Can be any integer between 2 and 36, inclusive.

Returns the numeric representation of the value with the given base, or 
nil
 if the conversion failed.

tostring
tostring(var: any): 
string

Name	Type	Description
var

any

The object to be converted to a string.

Returns the string representation of the value.

type
type(var: any): 
string

Name	Type	Description
var

any

The object to get the type of.

Returns the name of the object's type.

unpack
unpack(tbl: table, start_index: number, end_index: number): 
...

Name	Type	Description
tbl

table

The table to generate the vararg from.

start_index

number

Which index to start from. Optional.

end_index

number

Which index to end at. Optional, even if you set start_index.

xpcall
xpcall(func: function, err_callback: function[, ...]): 
boolean
, 
...

Name	Type	Description
func

function

The function to call initially.

err_callback

function

The function to be called if execution of the first fails. The error message is passed as a string.

...

any

Arguments to pass to the initial function.

Attempts to call the first function. If the execution succeeds, this returns 
true
 followed by the returns of the function. If execution fails, this returns 
false
 and the second function is called with the error message.

to_ticks
to_ticks(time: number): 
number

Name	Type	Description
time

number

The seconds to convert to ticks.

Converts time (seconds) to ticks.

to_time
to_time(ticks: number): 
number

Name	Type	Description
ticks

number

The number of ticks to convert to time.

Converts ticks to time (seconds).

new_class
ℹ️ This class system makes it easier to structure code in complex projects.

new_class(): 
metatable

Creates the new class.

Example1
Example2
Copy
local ctx = new_class()
    :struct 'struct_one' {
        variable1 = 'test',
        
        some_function = function(self, arg1)
            print(arg1)
            print(string.format('Hello World (%s)', self.variable1))
        end
    }
    
ctx.struct_one:some_function('test')
🗄️bit
Functions:
arshift
bit.arshift(x: number, n: number): 
number

Name	Type	Description
x

number

Number

n

number

Number of bits

Returns the bitwise arithmetic right-shift of its first argument by the number of bits given by the second argument. Arithmetic right-shift treats the most-significant bit as a sign bit and replicates it. Only the lower 5 bits of the shift count are used (reduces to the range [0..31]).

band
bit.band(x1: number, x2: number[, ...]): 
number

Name	Type	Description
x1

number

Number

x2

number

Number

...

Number(s)

Returns the bitwise and of all of its arguments. Note that more than two arguments are allowed.

bnot
bit.bnot(x: number): number

Name	Type	Description
x

number

Number

Returns the bitwise not of its argument.

bor
bit.bor(x1: number, x2: number[, ...]): 
number

Name	Type	Description
x1

number

Number

x2

number

Number

...

Number(s)

Returns the bitwise or of all of its arguments. Note that more than two arguments are allowed.

bswap
bit.bswap(x: number): number

Name	Type	Description
x

number

Number

Swaps the bytes of its argument and returns it. This can be used to convert little-endian 32 bit numbers to big-endian 32 bit numbers or vice versa.

bxor
bit.bxor(x1: number, [x2...]: number): 
number

Name	Type	Description
x1

number

Number

[x2...]

number

Number(s)

Returns the bitwise xor of all of its arguments. Note that more than two arguments are allowed.

lshift
bit.lshift(x: number, n: number): 
number

Name	Type	Description
x

number

Number

n

number

Number of bits

Returns the bitwise logical left-shift of its first argument by the number of bits given by the second argument. Logical shifts treat the first argument as an unsigned number and shift in 0-bits. Only the lower 5 bits of the shift count are used (reduces to the range [0..31]).

rol
bit.rol(x: number, n: number): 
number

Name	Type	Description
x

number

Number

n

number

Number of bits

Returns the bitwise left rotation of its first argument by the number of bits given by the second argument. Bits shifted out on one side are shifted back in on the other side. Only the lower 5 bits of the rotate count are used (reduces to the range [0..31]).

ror
bit.ror(x: number, n: number): 
number

Name	Type	Description
x

number

Number

n

number

Number of bits

Returns the bitwise right rotation of its first argument by the number of bits given by the second argument. Bits shifted out on one side are shifted back in on the other side. Only the lower 5 bits of the rotate count are used (reduces to the range [0..31]).

rshift
bit.rshift(x: number, n: number): 
number

Name	Type	Description
x

number

Number

n

number

Number of bits

Returns the bitwise logical right-shift of its first argument by the number of bits given by the second argument. Logical shifts treat the first argument as an unsigned number and shift in 0-bits. Only the lower 5 bits of the shift count are used (reduces to the range [0..31]).

tobit
bit.tobit(x: number): 
number

Name	Type	Description
x

number

Number to normalize

Normalizes a number to the numeric range for bit operations and returns it. This function is usually not needed since all bit operations already normalize all of their input arguments.

tohex
bit.tohex(x: number, n: number): 
number

Name	Type	Description
x

number

Number to convert

n

number

Number of hex digits to return

Converts its first argument to a hex string. The number of hex digits is given by the absolute value of the optional second argument. Positive numbers between 1 and 8 generate lowercase hex digits. Negative numbers generate uppercase hex digits. Only the least-significant 4*|n| bits are used. The default is to generate 8 lowercase hex digits.

🌈color
Example available!
⚙️
PAGE
Color
Functions:
:clone
color_object:clone(): 
color

Creates and returns a copy of the color object.

:init
RGBA
HEX
color_object:init(r: number, g: number, b: number, a: number): 
color

📌 Available overloads (RGBA):
color() -> 255, 255, 255, 255
color(
200
) -> 
200
, 
200
, 
200
, 255
color(
255
, 
160
) -> 
255
, 
255
, 
255
, 
160

color(
255
, 
195
, 
25
) -> 
255
, 
195
, 
25
, 255
color(
255
, 
195
, 
25
, 
200
) -> 
255
, 
195
, 
25
, 
200

Name	Type	Description
r

number

New R color range

g

number

New G color range

b

number

New B color range

a

number

New A color range

Overwrites the color's ranges. Returns itself.

:as_fraction
color_object:as_fraction(r: number, g: number, b: number, a: number): 
color

Name	Type	Description
r

number

New R color range as a percentage in the range [0.0, 1.0]

g

number

New G color range as a percentage in the range [0.0, 1.0]

b

number

New B color range as a percentage in the range [0.0, 1.0]

a

number

New A color range as a percentage in the range [0.0, 1.0]

Overwrites the color's ranges using the fraction values. Returns itself.

:as_int32
color_object:as_int32(value: number): 
color

Name	Type	Description
value

number

int32 color value

Overwrites the color's ranges converting the int32 value to RGBA values. Returns itself.

:as_hsv
color_object:as_hsv(h: number, s: number, v: number, a: number): 
color

Name	Type	Description
h

number

Hue color range [0.0, 1.0]

s

number

Saturation color range [0.0, 1.0]

v

number

Value color range [0.0, 1.0]

a

number

Alpha color range [0.0, 1.0]

Overwrites the color's ranges converting the HSV to RGBA values. Returns itself.

:as_hsl
color_object:as_hsl(h: number, s: number, l: number, a: number): 
color

Name	Type	Description
h

number

Hue color range [0.0, 1.0]

s

number

Saturation color range [0.0, 1.0]

l

number

Lightness color range [0.0, 1.0]

a

number

Alpha color range [0.0, 1.0]

Overwrites the color's ranges converting the HSL to RGBA values. Returns itself.

:to_fraction
color_object:to_fraction(): 
number
, 
number
, 
number
, 
number

Returns the r, g, b, and a ranges of the color as a percentage in the range of [0.0, 1.0].

:to_hex
color_object:to_hex(): 
string

Returns the HEX string representing the color.

:to_int32
color_object:to_int32(): 
number

Returns the int32 value representing the color.

:to_hsv
color_object:to_hsv(): 
number
, 
number
, 
number

Returns the HSV representation of the color.

:to_hsl
color_object:to_hsl(): 
number
, 
number
, 
number

Returns the HSL representation of the color.

:lerp
color_object:lerp(other: color, weight: number): 
color

Name	Type	Description
other

color

The color to interpolate to

weight

number

A value between 0 and 1 that indicates the weight of other

Returns the linearly interpolated color between two colors by the specified weight.

:grayscale
color_object:grayscale([ weight: number ]): 
color

Name	Type	Description
weight

number

Optional. A value between 0 and 1 that indicates the weight of grayscale

Returns the grayscaled color.

:alpha_modulate
color_object:alpha_modulate(alpha: number): 
color

Name	Type	Description
alpha

number

Alpha color range [0, 255]

Returns the current color with an overridden Alpha color range.

:unpack
color_object:unpack(): 
number
, 
number
, 
number
, 
number

Returns the r, g, b, and a values of the color. Note that these fields can be accessed by indexing r, g, b, and a.

Functions:
get_date
Logo
Date & time formats cheatsheet
Devhints.io cheatsheets
common.get_date(format: string[, unix_time: number]): 
string

Name	Type	Description
format

string

Date format (strftime)

unix_time

number

Optional. Unix-format time

Returns the formatted date.

get_unixtime
common.get_unixtime(): 
number

Returns the number of seconds that have elapsed since the unix epoch (1 January 1970 00:00:00)

get_timestamp
common.get_timestamp(): 
number

Returns high precision timestamp in milliseconds.

get_system_time
common.get_system_time(): 
table

Returns the windows time as a table containing the 
year
, 
month
, 
day
, 
hours
, 
minutes
, and 
seconds
 values.

get_product_version
common.get_product_version(): 
number

Returns the product version of the game client.

get_game_directory
common.get_game_directory(): 
string

Returns the path to the game client folder.

get_map_data
common.get_map_data(): 
table

Returns a table containing the 
name
, 
shortname
, and 
group
 values.

get_username
common.get_username(): 
string

Returns your Neverlose username.

get_config_name
common.get_config_name(): 
string

Returns the name of the currently loaded config.

get_active_scripts
common.get_active_scripts(): 
table

Returns a table of strings containing the names of the loaded scripts.

get_mouse_wheel_delta
common.get_mouse_wheel_delta(): 
number

Returns a value that indicates the amount that the mouse wheel has changed.

is_in_thirdperson
common.is_in_thirdperson(): 
boolean

Returns 
true
 if the camera is in thirdperson.

reload_script
common.reload_script()

Reloads current script.

unload_script
common.unload_script()

Unloads current script.

force_full_update
common.force_full_update()

Forces the server to send a full update packet.

set_clan_tag
common.set_clan_tag(text: string)

Name	Type	Description
text

string

New clan tag

Sets your in-game clan tag.

set_name
common.set_name(text: string)

Name	Type	Description
text

string

New name

Sets your in-game name.

add_event
common.add_event(text: string[, icon_name: string])

Name	Type	Description
text

string

Text to print to into the upper-left panel.

icon_name

string

Optional. Fontawesome icon name.

Prints the text into the upper-left neverlose event panel.

add_notify
common.add_notify(title: string, text: string)

Name	Type	Description
title

string

Text to print to into the title.

body

string

Text to print to into the body of the notification.

Draws the notification.

is_button_down
Logo
Virtual-Key Codes (Winuser.h) - Win32 apps
docsmsft
common.is_button_down(key: number): 
boolean

Name	Type	Description
key

number

Key to check

Returns 
true
 if the button is down, or nil on failure.

is_button_released
common.is_button_released(key: number): 
boolean

Name	Type	Description
key

number

Key to check

Returns 
true
 if the button is released, or nil on failure.
🎲cvar
Example
Copy
cvar.mp_teammates_are_enemies:int(1)
cvar.clear:call()
Functions:
:call
cvar_object:call(...)

Name	Type	Description
...

Arguments passed to the callback

Executes a ConCommand or cvar callback, passing its arguments to it.

:int
Get value
Set value
cvar_object:int(): 
number

Gets or sets the ConVar int value.

:float
Get value
Set value
cvar_object:float(): 
number

Gets or sets the ConVar float value.

:string
cvar_object:string([ value: any ]): 
string

Name	Type	Description
value

any

New string value. If not specified then returns the string value of the ConVar

Gets or sets the ConVar string value.

:set_callback
ConVar
Command
You can access the cvar_object, old_value and new_value by adding them to the function arguments.

cvar_object:set_callback(callback: function)

Name	Type	Description
callback

function

Lua function to call

Registers the callback to the specified ConVar/Command. The registered function will be called every time the specified convar value is updated.

:unset_callback
cvar_object:unset_callback(callback: function)

Name	Type	Description
callback

function

Lua function that was passed to the 
:set_callback
 function

Unregisters the callback that was set via the 
:set_callback
 function from the specified ConVar/Command.
👾entity
Functions:
get
entity.get(idx: number[, by_userid: boolean]): 
entity

Name	Type	Description
idx

number

Index of the entity

by_userid

boolean

If 
true
 then idx will be perceived as a userid

Returns a pointer to the specified entity.

get_local_player
entity.get_local_player(): 
entity

Returns a pointer to the local player.

get_players
entity.get_players([enemies_only: boolean, include_dormant: boolean, callback: function]):
table

Name	Type	Description
enemies_only

boolean

If 
true
 then only enemies will be included

include_dormant

boolean

If 
true
 then dormant players will be included

callback

function

A callback with an entity pointer as the argument

If the callback is nil, it returns the table of pointers to player entities. Otherwise the callback will be called. Access the player pointer using the arguments of the specified callback.

get_entities
entity.get_entities([class: number/string, include_dormant: boolean, callback: function]): 
table

Name	Type	Description
class

number/string

Either a name or an ID of the needed class. Pass 
nil
 to get every entity.

include_dormant

boolean

If 
true
 then dormant players will be included

callback

function

A callback with an entity pointer as the argument

If the callback is nil, it returns the table of pointers to entities. Otherwise the callback will be called. Access the entity pointer using the arguments of the specified callback.

get_threat
entity.get_threat([ hittable: boolean ]): 
entity

Name	Type	Description
hittable

boolean

If 
true
 then returns a pointer to the player that can hit you

Returns a pointer to the current threat.

get_game_rules
entity.get_game_rules(): 
entity

Returns the pointer to the CCSGameRulesProxy instance, or nil if none exists.

get_player_resource
entity.get_player_resource(): 
entity

Returns the pointer to the CCSPlayerResource instance, or nil if none exists.

Netprops
Getting FFI pointer
ent[0] : 
userdata

Returns the 
ffi
 pointer to the entity.

Getting netprop values
ent.prop_name: 
any

ent.prop_name[index]: 
any

ent["prop_name"]: 
any

Name	Type	Description
prop_name

string

Name of the networked property

index

number

Optional. If prop_name is an array, the value at this array index will be returned

Copy
local on_createmove = function(cmd)
    local localplayer = entity.get_local_player()
    
    if localplayer == nil then
        return
    end
    
    -- example 1
    local health = localplayer.m_iHealth

    -- example 2 [array netprops]
    local pitch = localplayer.m_flPoseParameter[12]

    -- example 3
    local stamina = localplayer['m_flStamina']
    
    print(('my health is: %d | pitch: %.1f | stamina: %d%%'):format(
        health, pitch,
        100 - (80 / 100 * stamina)
    ))
end

events.createmove:set(on_createmove)
Setting netprop values
ent.prop_name = value

ent.prop_name[index] = value

ent["prop_name"] = value

Name	Type	Description
prop_name

string

Name of the networked property

value

any

The property will be set to this value

index

number

Optional. If prop_name is an array, the value at this array index will be set

Copy
for _, player in ipairs(entity.get_players(true)) do
  -- example 1
  player.m_bSpotted = true
  
  -- example 2 [array netprops]
  player.m_flPoseParameter[12] = 0.5
  
  -- example 3
  player['m_nSkin'] = 2
end
Common
:is_player
ent:is_player(): 
boolean

Returns 
true
 if the entity is a player entity.

:is_weapon
ent:is_weapon(): 
boolean

Returns 
true
 if the entity is a weapon entity.

:is_dormant
ent:is_dormant(): 
boolean

Returns 
true
 if the entity is dormant.

:is_bot
ent:is_bot(): 
boolean

Returns 
true
 if the entity is a bot.

:is_alive
ent:is_alive(): 
boolean

Returns 
true
 if the entity is alive.

:is_enemy
ent:is_enemy(): 
boolean

Returns 
true
 if the entity is an enemy.

:is_visible
ent:is_visible(): 
boolean

Returns 
true
 if the entity is visible.

:is_occluded
ent:is_occluded([ to_entity: entity ]): 
boolean

Name	Type	Description
to_entity

entity

Optional. The entity that will be checked for occlusion

If the to_entity is nil, the local player is checked. Returns 
true
 if the entity is completely occluded for the current entity.

:get_index
ent:get_index(): 
number

Returns the index of the entity.

:get_name
ent:get_name(): 
string

Returns the player name, weapon name or class name if the entity is neither of those.

:get_origin
ent:get_origin(): 
vector

Returns the position vector of the entity.

:get_angles
ent:get_angles(): 
vector

Returns the absolute angles of the entity.

:get_simulation_time
ent:get_simulation_time(): 
table

Returns a table containing 
current
 and 
old
 simulation time values.

:get_classname
ent:get_classname(): 
string

Returns the name of the entity's class.

:get_classid
ent:get_classid(): 
number

Returns the ID of the entity's class.

:get_materials
ent:get_materials(): 
table

Returns a table containing all materials used by the entity.

:get_model_name
ent:get_model_name(): 
string

Returns the model name of the entity.

Players
:get_network_state
ent:get_network_state(): 
number

Returns the network state of the player.

ID	Description
0

The entity is 
not dormant

1

The entity is dormant but the cheat has 100% info where the player is

2

The entity is dormant (updated by 
Shared ESP
)

3

The entity is dormant (updated by 
Sounds
)

4

The entity is dormant (not updated)

5

The entity is dormant (data is 
unavailable
 or 
too old
)

:get_bbox
ent:get_bbox(): 
table

Returns a table containing 
pos1
, 
pos2
, and 
alpha
 values.

:get_player_info
ent:get_player_info(): 
table

Returns a table containing information from the player_info_t structure of the entity.

Table values: 
is_hltv
, 
is_fake_player
, 
steamid
, 
steamid64
, 
userid
, and 
files_downloaded

:get_player_weapon
ent:get_player_weapon([all_weapons: boolean]): 
entity / table

Name	Type	Description
all_weapons

boolean

If 
true
 then all weapons will be included

Returns a pointer to the player's weapon entity.

If all_weapons is 
true
, returns a table containing pointers to every weapon entity the player is currently carrying.

:get_anim_state
ent:get_anim_state(): 
table

Returns a table containing information about the animation state of the player.

:get_anim_overlay
ent:get_anim_overlay([idx: number]): 
table

Name	Type	Description
idx

number

Index of the animation layer

Returns a table containing information about the specified animation layer. Pass nil to get every animation layer.

:get_eye_position
ent:get_eye_position(): 
vector

Returns the eye position of the player.

:get_bone_position
ent:get_bone_position(idx: number): 
vector

Name	Type	Description
idx

number

Index of the bone

Returns the position of the specified bone.

:get_hitbox_position
ent:get_hitbox_position(idx: number): 
vector

Name	Type	Description
idx

number

Index of the hitbox

Returns the position of the specified hitbox.

:get_steam_avatar
ent:get_steam_avatar(): 
ImgObject

Returns a pointer to the Steam avatar image object of the specified entity.

:get_xuid
ent:get_xuid(): 
string

Returns the Steam ID of the player.

:get_resource
ent:get_resource(): 
entity

Returns the pointer to the CCSPlayerResource instance attached to the player, or nil if none exists.

:get_spectators
ent:get_spectators(): 
table

Returns a table of pointers to the players that are currently spectating the specified player.

:set_icon
ent:set_icon([icon: string])

Name	Type	Description
icon

string

Optional. URL to the icon or a panorama path.

Sets an icon in the scoreboard next to the specified player's avatar. The icon will be removed if no icon was provided.

:simulate_movement
ent:simulate_movement([origin: vector, velocity: vector, flags: number]): 
sim_ctx

Name	Type	Description
origin

vector

Specifies the origin from which the movement should be simulated. If not provided, it uses the player's current origin.

velocity

vector

Specifies the velocity vector for the simulated movement. If not provided, the function will use the player's current velocity as the default for the simulation.

flags

number

Specifies the m_fFlags 32-bit mask for prediction. If not provided, it uses the player's current m_fFlags value.

This function allows you to simulate players' movement by optionally providing an origin, velocity, and flags. Returns an instance of the 
sim_ctx
 class containing details and tools for the movement simulation.

Simulation Context
This class encapsulates the context and results of a movement simulation initiated by :simmulate_movement.

:think
sim:think([ticks: number])

Simulates the player's movement for a specified number of ticks. If not specified, it defaults to simulating for 1 tick.

Name	Type	Description
origin

vector

Position of the player after simulation.

velocity

vector

Velocity of the player after simulation.

view_offset

number

Z axis view offset. Used to calculate the eye position.

duck_amount

number

m_flDuckAmount value of the player after simulation.

did_hit_collision

boolean

Flags whether the player hit a collision during the simulation.

obb_mins

vector

Player's bounding box's minimum points.

obb_maxs

vector

Player's bounding box's maximum points.

move

vector

simulation_ticks

number

The number of ticks over which the simulation was conducted.

gravity_per_apply

number

Indicates the applied gravitational force during the simulation.

original_max_speed

number

The player's maximum speed before the simulation.

max_speed

number

The maximum speed achieved by the player during the simulation.

is_speed_cropped

boolean

velocity_modifier

number

m_flVelocityModifier value of the player after simulation.

duck_speed

number

The simulated speed at which the player can be crouching.

stamina

number

m_flStaminavalue of the player after simulation.

surface_friction

number

m_surfaceFriction value of the player after simulation.

trace

trace

Post-simulation trace object.

Weapons
Access functions listed below via 
:get_player_weapon
 function

:get_weapon_index
ent:get_weapon_index(): 
number

Returns the index of the weapon.

:get_weapon_icon
ent:get_weapon_icon(): 
ImgObject

Returns the icon of the weapon.

:get_weapon_info
ent:get_weapon_info(): 
userdata

Returns a pointer to the 
CCSWeaponInfo
 struct of the weapon.

:get_weapon_owner
ent:get_weapon_owner(): 
entity

Returns a pointer to the weapon owner's entity.

:get_weapon_reload
ent:get_weapon_reload(): 
number

Returns the weapon reload percentage (0.0-1.0), -1 if not reloading. 

:get_max_speed
ent:get_max_speed(): 
number

Returns the maximum speed the player can move with the weapon.

:get_spread
ent:get_spread(): 
number

Returns the spread of the weapon in radians.

:get_inaccuracy
ent:get_inaccuracy(): 
number

Returns the inaccuracy of the weapon in radians.
Example available!
⚙️
PAGE
ESP
Class names
Available ESP classes: 
enemy
, 
team
, 
self

Functions:
:new_text
esp.esp_class:new_text(name: string, preview: string, callback: function): 
ESPGroup

Name	Type	Description
name

string

ESP element picker text

preview

string

ESP preview text

callback

function

Function that will be called for each entity while drawing the ESP

Registers ESP text to the specified class. The callback function is called every frame. It is passed an entity pointer. Return a string in order to manage the output.

:new_bar
esp.esp_class:new_bar(name: string, callback: function): 
ESPGroup

Name	Type	Description
name

string

ESP element picker text

callback

function

Function that will be called for each entity while drawing the ESP

Registers an ESP bar to the specified class. The callback function is called every frame. Access the entity pointer using the arguments of the specified callback. Return a boolean followed by the number in the range [0.0, 1.0].

:new_item
esp.esp_class:new_item(name: string): 
ESPGroup

Name	Type	Description
name

string

ESP element picker text

Registers an ESP item that is neither text nor a bar.

🔗 struct 
ESPGroup
Name	Type	Description
get

function

Returns the value of the item.

set

function

Sets the value of the item.

name

function

Returns the name of the item. If the argument is present, the name is set to the new value.

create

function

Attaches a group to the current item.
🗳️events
Functions:
Available cheat events can be found here.

:set
events.event_name:set(callback: function)

Name	Type	Description
callback

function

Lua function to call

Sets the callback for the specified event. The registered function will be called every time the specified event occurs.

:unset
events.event_name:unset(callback: function)

Name	Type	Description
callback

function

Lua function that was passed to the 
:set
 function

Unsets the callback that was set via the 
:set
 function from the specified event.

:call
events.event_name:call(...)

Name	Type	Description
...

Arguments to be passed by the callback

Fires the specified event.

Alternative behavior:
:__call
events.event_name(callback: function[, state: boolean])

Name	Type	Description
callback

function

Lua function to call

state

boolean

Optional. Callback state. If not specified then toggles the callback state for the specified function.

Sets / unsets the callback for the specified event.

Toggle Behavior
State Behavior
Copy
local function function_callback()
    print(globals.tickcount)
end

-- Sets the createmove callback for the specified function
events.createmove(function_callback)

-- Execute after 0.5 secs
utils.execute_after(0.5, function()
    -- Toggles the createmove callback for the specified function
    events.createmove(function_callback)
end)
📂files
Example available!
Functions:
create_folder
files.create_folder(path: string)

Name	Type	Description
path

string

New folder path

read
files.read(path: string): 
any

Name	Type	Description
path

string

Path to the file

Returns contents of the specified file.

write
files.write(path: string, contents: string[, is_binary: boolean]): 
boolean

Name	Type	Description
path

string

Path to the file

contents

any

Contents the file should be set to

is_binary

boolean

Is 
contents
 a binary

Replaces contents of the specified file. Returns 
false
 on failure.

get_crc32
files.get_crc32(path: string): 
number

Name	Type	Description
path

string

Path to the file
📎globals
Variables:
curtime
globals.curtime : 
number

Server time in seconds.

realtime
globals.realtime : 
number

Local time in seconds.

frametime
globals.frametime : 
number

Duration of the last game frame in seconds.

framecount
globals.framecount : 
number

Amount of frames since the game started.

absoluteframetime
globals.absoluteframetime : 
number

Duration of the last game frame in seconds.

tickcount
globals.tickcount : 
number

Number of ticks elapsed on the server.

tickinterval
globals.tickinterval : 
number

Duration of a tick in seconds.

max_players
globals.max_players : 
number

Maximum number of players on the server.

is_connected
globals.is_connected : 
boolean

Returns 
true
 if the player is connected, but not necessarily active in game (could still be loading).

is_in_game
globals.is_in_game : 
boolean

Returns 
true
 if the player is currently connected to a game server.

choked_commands
globals.choked_commands : 
number

Number of choked commands.

commandack
globals.commandack : 
number

Current command number acknowledged by server.

commandack_prev
globals.commandack_prev : 
number

Sequence number of last outgoing command.

last_outgoing_command
globals.last_outgoing_command : 
number

Number of last command sequence number acknowledged by server.

server_tick
globals.server_tick : 
number

Last-received tick from the server.

client_tick
globals.client_tick : 
number

The client's own tick count.

delta_tick
globals.delta_tick : 
number

Last-valid received snapshot (server) tick.

clock_offset
globals.clock_offset : 
number

Difference between the server and client tick counts, used to predict the current server tick count.

🔓json
Functions:
parse
json.parse(json_text: string): 
any

Argument	Type	Description
json_text

string

UTF-8 JSON text

Will deserialize any UTF-8 JSON string into a Lua value or table.

stringify
json.stringify(value: any): 
string

Argument	Type	Description
value

any

A lua boolean, number, string, table or nil

Will serialize a Lua value into a string containing the JSON representation.

✨materials
Example available!
⚙️
PAGE
Materials
Functions:
get
materials.get(path: string[, force_load: boolean]): 
Material

Name	Type	Description
path

string

Directory to the specified material

force_load

force_load

Loads the material if not loaded

Returns the material object in the specified path.

get_materials
materials.get_materials(partial_path: string[, force_load: boolean, callback: function]): 
table

Name	Type	Description
partial_path

string

Directory to the specified materials

force_load

force_load

Loads each material if not loaded

callback

function

A callback with a pointer to the material object as the argument

If the callback is nil, it returns the table of material objects along the specified path. Otherwise the callback will be called. Access the material object using the arguments of the specified callback.

create
materials.create(name: strings, key_values: string): 
Material

Name	Type	Description
name

string

New material name

key_values

string

New material values

Creates and returns a new material object

🔗 struct 
Material
:get_name
material:get_name(): 
string

Returns the name of the material.

:get_texture_group_name()
material:get_texture_group_name(): 
string

Returns the texture group name of the material.

:var_flag
material:var_flag(flag: number[, value: boolean]): 
boolean

Name	Type	Description
flag

number

Material var flag

value

boolean

New material var flag value

Gets or sets the value of the material var flag.

:shader_param
material:shader_param(name: string[, value: any]): 
any

Name	Type	Description
name

string

Shader parameter name

value

any

New shader parameter value

Gets or sets the value of the material shader parameter.

:color_modulate
material:color_modulate([color: color])

Name	Type	Description
color

color

New color modulation value

Gets or sets the material color modulation value.

:alpha_modulate
material:alpha_modulate([alpha: number])

Name	Type	Description
alpha

number

New alpha modulation value

Gets or sets the material alpha modulation value.

:is_valid
material:is_valid(): 
boolean

Returns 
true
 if the material is valid.

:reset
material:reset()

Resets the material properties to its original values along with discarding the override.

:override
material:override(mat: Material)

Name	Type	Description
mat

Material

Material object with the needed properties

Overrides material properties to properties from another material without setting them.

🔢math
Functions:
abs
math.abs(x: number): 
number

Name	Type	Description
x

number

Number

Returns the absolute value of x.

acos
math.acos(x: number): 
number

Name	Type	Description
x

number

Number

Returns the arc cosine of x (in radians).

asin
math.asin(x: number): 
number

Name	Type	Description
x

number

Number

Returns the arc sine of x (in radians).

atan
math.atan(x: number): 
number

Name	Type	Description
x

number

Number

Returns the arc tangent of x (in radians).

atan2
math.atan2(x: number, y: number): 
number

Name	Type	Description
x

number

Number

y

number

Number

Returns the arc tangent of y/x (in radians), but uses the signs of both parameters to find the quadrant of the result. (It also handles correctly the case of x being zero.)

ceil
math.ceil(x: number): 
number

Name	Type	Description
x

number

Number

Returns the smallest integer larger than or equal to x.

clamp
math.clamp(value: number[, min: number, max: number]): 
number

Name	Type	Description
value

number

The value to clamp

min

number

The minimum value

max

number

The maximum value

Returns the clamped value.

cos
math.cos(x: number): 
number

Name	Type	Description
x

number

Number

Returns the cosine of x (assumed to be in radians).

cosh
math.cosh(x: number): 
number

Name	Type	Description
x

number

Number

Returns the hyperbolic cosine of x.

deg
math.deg(x: number): 
number

Name	Type	Description
x

number

Number

Returns the angle x (given in radians) in degrees.

exp
math.exp(x: number): 
number

Name	Type	Description
x

number

Number

Returns the value e power x.

floor
math.floor(x: number): 
number

Name	Type	Description
x

number

Number

Returns the largest integer smaller than or equal to x.

fmod
math.fmod(x: number, y: number): 
number

Name	Type	Description
x

number

Number

y

number

Number

Returns the remainder of the division of x by y that rounds the quotient towards zero.

frexp
math.frexp(x: number): 
number
, 
number

Name	Type	Description
x

number

Number

𝑥
=
𝑚
2
𝑒
x=m2 
e
 
Returns m and e such that x = m2e, e is an integer and the absolute value of m is in the range [0.5, 1) (or zero when x is zero).

huge
math.huge : 
number

The value HUGE_VAL, a value larger than or equal to any other numerical value.

ldexp
math.ldexp(x: number, e: number): 
number

Name	Type	Description
x

number

Number

e

number

Number

𝑜
𝑢
𝑡
𝑝
𝑢
𝑡
=
𝑚
2
𝑒
output=m2 
e
 
Returns m2e (e should be an integer).

log
math.log(x: number): 
number

Name	Type	Description
x

number

Number

Returns the natural logarithm of x.

log10
math.log10(x: number): 
number

Name	Type	Description
x

number

Number

Returns the base-10 logarithm of x.

map
math.map(value: number, in_from: number, in_to: number, out_from: number, out_to: number[, should_clamp: boolean]): 
number

Name	Type	Description
value

number

The value to map

in_from

number

In minimum value

in_to

number

In maximum value

out_from

number

Out minimum value

out_to

number

Out maximum value

should_clamp

boolean

Clamp In range

Linearly maps two number ranges and returns the mapped value.

max
math.max(x: number[, ...]): 
number

Name	Type	Description
x

number

Number

...

Comma-separated numbers to concatenate with x

Returns the maximum value among its arguments.

min
math.min(x: number[, ...]): 
number

Name	Type	Description
x

number

Number

...

Comma-separated numbers to concatenate with x

Returns the minimum value among its arguments.

modf
math.modf(x: number): 
number

Name	Type	Description
x

number

Number

Returns two numbers, the integral part of x and the fractional part of x.

normalize_yaw
math.normalize_yaw(x: number): 
number

Name	Type	Description
x

number

Number

Returns the normalized yaw angle value.

pi
math.pi : 
number

The value of pi.

pow
math.pow(x: number, y: number): 
number

Name	Type	Description
x

number

Number

y

number

Number

Returns x^y. (You can also use the expression x^y to compute this value.)

rad
math.rad(x: number): 
number

Name	Type	Description
x

number

Number

Returns the angle x (given in degrees) in radians.

random
math.random([m [, n]]): 
number

Name	Type	Description
m

number

Number

n

number

Number

This function is an interface to the simple pseudo-random generator function rand provided by ANSI C.When called without arguments, returns a uniform pseudo-random real number in the range [0,1). When called with an integer number m, math.random returns a uniform pseudo-random integer in the range [1, m]. When called with two integer numbers m and n, math.random returns a uniform pseudo-random integer in the range [m, n].

randomseed
math.randomseed(x: number): 
number

Name	Type	Description
x

number

Number

Sets x as the "seed" for the pseudo-random generator: equal seeds produce equal sequences of numbers.

sin
math.sin(x: number): 
number

Name	Type	Description
x

number

Number

Returns the sine of x (assumed to be in radians).

sinh
math.sinh(x: number): 
number

Name	Type	Description
x

number

Number

Returns the hyperbolic sine of x.

sqrt
math.sqrt(x: number): 
number

Name	Type	Description
x

number

Number

Returns the square root of x. (You can also use the expression x^0.5 to compute this value.)

tan
math.tan(x: number): 
number

Name	Type	Description
x

number

Number

Returns the tangent of x (assumed to be in radians).

tanh
math.tanh(x: number): 
number

Name	Type	Description
x

number

Number

Returns the hyperbolic tangent of x.

💻ui
Example available!
⚙️
PAGE
UI
Functions:
get_alpha
ui.get_alpha(): 
number

Returns the menu opacity as a unit interval (value in the range [0, 1]).

get_size
ui.get_size(): 
vector

Returns the current menu size.

get_position
ui.get_position(): 
vector

Returns the current menu position.

get_mouse_position
ui.get_mouse_position(): 
vector

Returns the current mouse position.

get_binds
ui.get_binds(): 
table

Returns a table of pointers to hotkeys.

struct 
Hotkey
Name	Type	Description
name

string

Hotkey name

mode

number

Hotkey mode (
1
: Hold, 
2
: Toggle)

value

any

Hotkey value

active

boolean

Hotkey state

reference

MenuItem

Pointer to the menu item

get_style
ui.get_style([ name: string ]): 
color
 / 
table

Returns the color of the Style Option. Pass nil to return a table with the style options.

get_icon
ui.get_icon(name: string): 
string

Returns the unicode converted string corresponding the fontawesome icon.

create
Group
Tab
ui.create(group: string): 
MenuGroup

Name	Type	Description
group

string

Group name


Creates and returns a menu group object.


find
Item example
Sub-item example
Group example
Global item example
ESP Item Example
ui.find(category: string, tab: string, group: string, item: string): 
MenuItem

Name	Type	Description
category

string

Category name, e.g. "Aimbot" or "Visuals".

tab

string

Tab name that belongs to the category.

group

string

Name of group with the item.

item

string

The needed item.

Returns the 
MenuItem
 object that corresponds to the specified path.

ui.find(category: string, tab: string, sub_tab: string, group: string, item: string): 
MenuItem

Name	Type	Description
category

string

Category name, e.g. "Aimbot" or "Visuals".

tab

string

Tab name that belongs to the category.

sub_tab

string

Sub-tab name.

group

string

Name of group with the item.

item

string

The needed item.

Returns the 
MenuItem
 object that corresponds to the specified path.

This can return multiple items or nil on failure.

sidebar
Logo
Search Icons & Find the Perfect Design | Font Awesome
fontawesome
ui.sidebar([name: string, icon_name: string]): 
string
, 
string

Name	Type	Description
name

string

Optional. Sidebar tab name

icon_name

string

Optional. Icon name (Brand icons are currently not supported)

Gets or sets the sidebar tab name and an icon.

localize
ui.localize(lang: string, str: string[, localized: string]): 
string
 / 
nil

Name	Type	Description
lang

string

Language code.

str

string

String to localize or to get the localized string from.

localized

string

Optional. The localized variant.

Returns the localized string for the specified language code. If localized is present, the str will be localized accordingly.

🔗 
MenuGroup
:switch
group:switch(name: string[, init: boolean]): 
MenuItem

Name	Type	Description
name

string

Item name

init

boolean

Default value

Creates and returns a menu item object, or throws an error on failure.

:slider
group:slider(name: string, min: number, max: number[, init: number, scale: number, tooltip: function]): 
MenuItem

Name	Type	Description
name

string

Item name

min

number

Minimum allowed value

max

number

Maximum allowed value

init

number

Default value

scale

number

Display value multiplier. Can be used for decimal places.

tooltip

string / function

A string appends itself to the display value. A function allows you to access the raw display value and displays anything it returns.

Creates and returns a menu item object, or throws an error on failure.

:combo
group:combo(name: string, items: any[, ...]): 
MenuItem

Name	Type	Description
name

string

Item name

items

any

One or more comma separated values that will be added to the combo. Alternatively, a table of strings that will be added

...

Creates and returns a menu item object, or throws an error on failure.

:selectable
group:selectable(name: string, items: any[, ...]): 
MenuItem

Name	Type	Description
name

string

Item name

items

any

One or more comma separated values that will be added to the combo. Alternatively, a table of strings that will be added

...

Creates and returns a menu item object, or throws an error on failure.

:color_picker
Simple
Multi-Color
group:color_picker(name: string[, color: color]): 
MenuItem

Name	Type	Description
name

string

Item name

color

color

Optional. Initial color value

Creates and returns a menu item object, or throws an error on failure.

:button
group:button(name: string[, callback: function, alt_style: boolean]): 
MenuItem

Name	Type	Description
name

string

Item name

callback

function

Optional. Function that will be called when the button is clicked

alt_style

boolean

Optional. Pass 
true
 to enable the alternative style for the specified button

Creates and returns a menu item object, or throws an error on failure.

:hotkey
Logo
Virtual-Key Codes (Winuser.h) - Win32 apps
docsmsft
group:hotkey(name: string[, default_key: number): 
MenuItem

Name	Type	Description
name

string

Item name

default_key

number

Optional. Default key

Creates and returns a menu item object, or throws an error on failure.

:input
group:input(name: string[, text: string]): 
MenuItem

Name	Type	Description
name

string

Item name

text

string

Optional. Default value

Creates and returns a menu item object, or throws an error on failure.

:list
group:list(name: string, items: any[, ...]): 
MenuItem

Name	Type	Description
name

string

Item name

items

any

One or more comma separated values that will be added to the combo. Alternatively, a table of strings that will be added

...

Creates and returns a menu item object, or throws an error on failure.

:listable 
group:listable(name: string, items: any[, ...]): 
MenuItem

Name	Type	Description
name

string

Item name

items

any

One or more comma separated values that will be added to the combo. Alternatively, a table of strings that will be added

...

Creates and returns a menu item object, or throws an error on failure.

:label
group:label(text: string): 
MenuItem

Name	Type	Description
text

string

Label text

Creates and returns a menu item object, or throws an error on failure.

:texture
📌 Create the texture via the :load_image function.

group:texture(texture: ImgObject[, size: vector, color: color, mode: string, rounding: number]): 
MenuItem

Name	Type	Description
texture

ImgObject

Image object

size

vector

Optional. Size of the image

color

color

Optional. Color of the texture

mode

string

Optional. 
f
 for fill, 
r
 for repeat

rounding

number

Optional. Image border rounding

Creates and returns a menu item object, or throws an error on failure.

🔗 
MenuItem
:get
item:get(): 
any

Returns the value of the menu item.

:id
item:id(): 
number

Returns the unique id of the menu item.

:list
item:list(): 
table

Returns the list of items. combo / selectable menu item objects only.

:type
item:type(): 
string

Returns the type of the menu item.

:override
item:override(value: any[, ...])

Name	Type	Description
value

any

The value to which the menu item will be set

...

Overrides the item value without changing the menu / config values. If the value argument is nil or missing, the override is undone.

:get_override
item:get_override(): 
any

Returns the value of the menu item it's overriden to.

:update
item:update(...)

Name	Type	Description
...

any

Updates the values of the menu item.

:reset
item:reset()

Resets the menu item to it's original value.

:set
item:set(value: any[, ...])

Name	Type	Description
value

any

The value to which the menu item will be set

...

Sets the value of the menu item.

:name
item:name([value: any])

Name	Type	Description
value

any

New name

Gets or sets the name of the menu item.

:tooltip
item:tooltip([value: any])

Name	Type	Description
value

any

New tooltip text

Gets or sets the tooltip of the menu item (depending on the presence of the value parameter).

:visibility
item:visibility([state: boolean])

Name	Type	Description
state

boolean

New visibility state

Gets or sets the menu item visibility depending on the value of state.

:disabled
item:disabled([state: boolean])

Name	Type	Description
state

boolean

New disabled state

Gets or sets the menu item disabled state depending on the value of state.

:set_callback
item:set_callback(callback: function[, force_call: boolean])

Name	Type	Description
callback

function

Function that will be called when the menu item is interacted with

force_call

boolean

Pass 
true
 to call the callback function after setup

Sets the callback to the specified menu item.

:unset_callback
item:unset_callback(callback: function)

Name	Type	Description
callback

function

Lua function that was passed to the 
:set_callback
 function

Unsets the callback that was set via the 
:set_callback
 function.

:color_picker
Simple
Multi-Color
item:color_picker([color: color]): 
MenuItem

Name	Type	Description
color

color

Optional. Initial color value

Attaches a color picker to the current menu item object.

:create
item:create(): 
MenuGroup

Attaches a group (gear) to the current menu item object.

:parent
item:parent(): 
MenuItem
 / 
MenuGroup

Returns the parent object of the menu item.
☁️network
Functions:
get
network.get(url: string[, headers: table, callback: function]): 
string

Name	Type	Description
url

string

URL

headers

table

Headers

callback

function

Callback

Sends a GET request to the URL.

post
network.post(url: string[, data: table, headers: table, callback: function]): 
string

Name	Type	Description
url

string

URL

data

table

Post data

headers

table

Headers

callback

function

Callback

Sends a POST request to the URL.
🌃panorama
Functions:
loadstring
panorama.loadstring(js_code: string[, panel: string]): 
any

Name	Type	Description
js_code

string

String containing JavaScript code

panel

string
 (panorama root panel)

Optional. Panel name

Accessing API's
panorama[api]: 
table

Name	Type	Description
api

string

API name, e.g. GameStateAPI

panorama[panel][api]: 
table

Name	Type	Description
panel

string
 (panorama root panel)

Panel name

api

string

API name, e.g. GameStateAPI

Copy
-- Access an API from the default root panel
local MyPersonaAPI = panorama.MyPersonaAPI

print(MyPersonaAPI.GetXuid())

-- Access an API from a specific root panel
local UiToolkitAPI = panorama.CSGOMainMenu.UiToolkitAPI

UiToolkitAPI.CloseAllVisiblePopups()

💢rage
Structs:
🔗 
antiaim
:get_max_desync
rage.antiaim:get_max_desync(): 
number

Returns the maximum amount of desync.

:get_rotation
rage.antiaim:get_rotation([value: boolean]): 
number

Name	Type	Description
value

boolean

Optional. If 
true
, fake rotation will be returned.

Returns the current anti-aim rotation.

:get_target
rage.antiaim:get_target([return_fr: boolean]): 
number

Name	Type	Description
return_fr

boolean

Optional. If 
true
, freestanding yaw will be returned.

Returns the at target yaw rotation or nil if not available.

:inverter
rage.antiaim:inverter([value: boolean]): 
boolean

Name	Type	Description
value

boolean

Optional. Inverter state.

Gets or sets the state of the anti-aim inverter.

:override_hidden_pitch
rage.antiaim:override_hidden_pitch(value: number): 
number

Name	Type	Description
value

number

Hidden pitch value.

Overrides the hidden pitch to the desired value.

:override_hidden_yaw_offset
rage.antiaim:override_hidden_yaw_offset(value: number): 
number

Name	Type	Description
value

number

Hidden yaw offset value.

Overrides the hidden yaw offset to the desired value.

🔗 
exploit
:get
rage.exploit:get(): 
number

Returns the exploit charge as a unit interval (value in the range [0, 1]).

:allow_charge
rage.exploit:allow_charge([value: boolean])

Name	Type	Description
value

boolean

Optional. If 
true
, allows exploit charge. If 
false
, blocks exploit charge. Defaults to 
true

Allows/blocks exploit charge.

:allow_defensive
rage.exploit:allow_defensive([value: boolean])

Name	Type	Description
value

boolean

Optional. If 
true
, allows the cheat to discharge defensive exploit. If 
false
, blocks defensive exploit discharge. Defaults to 
true

Allows/blocks defensive exploit discharge.

:force_teleport
rage.exploit:force_teleport()

:force_charge
rage.exploit:force_charge()
🎨render
Functions:
screen_size
render.screen_size(): 
vector

camera_position
render.camera_position(): 
vector

Returns the camera position vector.

camera_angles
render.camera_angles([angles: vector]): 
vector

Name	Type	Description
angles

vector

New camera angles

Returns or sets the camera angles.

world_to_screen
render.world_to_screen(position: vector): 
vector

Name	Type	Description
position

vector

Position in world space

📌 Note that there is a cleaner alternative, the :to_screen vector function.

Returns the screen position vector, or nil if the world position is not visible on your screen. This can only be called from the render callback.

get_offscreen
render.get_offscreen(position: vector, radius: number[, accurate: boolean]): 
vector
, 
number
, 
boolean

Name	Type	Description
position

vector

Position in world space

radius

number

Distance from the center of the screen as a percentage in the range [0.0, ∞]

accurate

boolean

Optional. If 
true
 then accurate calculations will be used

Returns the 
position
, 
rotation
, and 
is_out_of_fov
 arguments or nil on failure.

position
: Screen coordinates (Returns ellipse-based position if the world position is out of FOV)
rotation
: Yaw axis that can be used to rotate drawing stuff
is_out_of_fov
: Returns 
true
 if the world position is out of FOV.

get_pixel
render.get_pixel(position: vector)

Getting the color of the pixel is a heavy process. Do not do it inside callbacks that are called a lot of times per second.

Name	Type	Description
position

vector

Screen position

Returns the color of the specified pixel on the screen.

load_font
📌 Render any text via the draw:text function.

Size as a number
Size as a vector
render.load_font(name: string, size: number[, flags: string]): 
FontObject

Name	Type	Description
name

string

Font that will be initialized

size

number

Size of the font

flags

string

a
 for anti-aliasing,  
i
 for cursive text, and 
b
 for bold text, 
o
 for outlined text, 
d
 for the drop shadow effect.

Returns the FontObject struct or nil on failure.

load_image
📌 Render any image via the :texture function.

render.load_image(contents: string, size: vector): 
ImgObject

Name	Type	Description
contents

string

Raw image file contents

size

vector

Size of the image

Returns the ImgObject struct or nil on failure. Supports JPG, PNG, BMP, SVG, and GIF formats.

load_image_rgba
📌 Render any image via the :texture function.

render.load_image_rgba(contents: string, size: vector): 
ImgObject

Name	Type	Description
contents

string

RGBA buffer (HEX encoded)

size

vector

Size of the image

Returns the ImgObject struct or nil on failure.

load_image_from_file
📌 Render any image via the :texture function.

render.load_image_from_file(path: string, size: vector): 
ImgObject

Name	Type	Description
path

string

Path to the image

size

vector

Size of the image

ℹ️ Loading images from game resources is supported.

Example: render.load_image_from_file 'materials/panorama/images/icons/ui/warning.svg'

Returns the ImgObject struct or nil on failure. Supports JPG, PNG, BMP, SVG, and GIF formats.

measure_text
render.measure_text(font: FontObject[, flags: string], text: string): 
vector

Name	Type	Description
font

FontObject

Font object or 
1
 for 
Default
 font, 
2
 for 
Small
 font, or 
3
 for 
Console
 font

flags

string

Optional. 
s
 for DPI scaled text

text

string

Text that will be measured

Returns the measured size of the text.

highlight_hitbox
render.highlight_hitbox(entity: entity, hitbox: number, color: color)

Name	Type	Description
entity

entity

The player whose hitbox(es) are to be highlighted.

hitbox

number

Hitbox index (an integer between 0 and 18). A table with hitbox indices can also be used to highlight multiple hitboxes. Pass 19 to highlight every hitbox.

color

color

The color with which you want to highlight the hitbox(es).

Highlights the specified hitbox / hitboxes.

get_scale
render.get_scale(type: number): 
number

Name	Type	Description
type

number

The type of DPI scale to return.

1
 - Menu Scale, 
2
 - ESP Scale.

Returns the DPI scale value.

Structs
🔗 
ImgObject
width
img.width : 
number

height
img.height : 
number

resolution
img.resolution : 
number

🔗 
FontObject
width
font.width : 
number

height
font.height : 
number

spacing
font.spacing : 
number

:set_size
Size as a number
Size as a vector
font:set_size(size: number)

Name	Type	Description
size

number

New size of the font

Sets the new font size.

Draw functions
blur
render.blur(position_a: vector, position_b: vector, strength: number, alpha: number[, rounding: number])

Name	Type	Description
position_a

vector

Start position

position_b

vector

End position

strength

number

Blur strength

alpha

number

Alpha percentage in the range [0.0, 1.0]

rounding

number

Optional. Rounding of the blur rectangle in pixels

line
render.line(position_a: vector, position_b: vector, color: color)

Name	Type	Description
position_a

vector

Start position

position_b

vector

End position

color

color

Color of the line

poly
render.poly(color: color, positions: vector[, ...])

Name	Type	Description
color

color

Color of the polyline

positions

vector

Screen positions

...

Comma-separated vectors to concatenate with positions

poly_blur
render.poly_blur(opacity: number, strength: number, positions: vector[, ...])

Name	Type	Description
opacity

number

Opacity percentage in the range [0.0, 1.0]

strength

number

Blur strength

positions

vector

Screen positions

...

Comma-separated vectors to concatenate with positions

poly_line
render.poly_line(color: color, positions: vector[, ...])

Name	Type	Description
color

color

Color of the polyline

positions

vector

Screen positions

...

Comma-separated vectors to concatenate with positions

rect
render.rect(position_a: vector, position_b: vector, color: color[, rounding: number, no_clamp: boolean])

Name	Type	Description
position_a

vector

Start position

position_b

vector

End position

color

color

Color of the rectangle

rounding

number

Optional. Rounding of the rectangle in pixels

no_clamp

boolean

Optional. If 
true
, negative sizes will be allowed

rect_outline
render.rect_outline(position_a: vector, position_b: vector, color: color[, thickness: number, rounding: number, no_clamp: boolean])

Name	Type	Description
position_a

vector

Start position

position_b

vector

End position

color

color

Color of the rectangle

thickness

number

Optional. Thickness of the rectangle in pixels

rounding

number

Optional. Rounding of the rectangle in pixels

no_clamp

boolean

Optional. If 
true
, position_a < position_b will be allowed

gradient
render.gradient(position_a: vector, position_b: vector, top_left: color, top_right: color, bottom_left: color, bottom_right: color[, rounding: number])

Name	Type	Description
position_a

vector

Start position

position_b

vector

End position

top_left

color

Color of the top left rectangle position

top_right

color

Color of the top right rectangle position

bottom_left

color

Color of the bottom left rectangle position

bottom_right

color

Color of the bottom right rectangle position

rounding

number

Optional. Rounding of the gradient in pixels

circle
render.circle(position: vector, color: color, radius: number, start_deg: number, pct: number)

Name	Type	Description
position

type

Screen position

color

color

Color of the circle

radius

number

Radius of the circle in pixels

start_deg

number

0
 is the right side, 
90
 is the bottom, 
180
 is the left, 
270
 is the top

pct

number

Percentage in the range [0.0-1.0] determining how full the circle is

circle_outline
render.circle_outline(position: vector, color: color, radius: number, start_deg: number, pct: number[, thickness: number])

Name	Type	Description
position

vector

Screen position

color

color

Color of the circle

radius

number

Radius of the circle in pixels

start_deg

number

0
 is the right side, 
90
 is the bottom, 
180
 is the left, 
270
 is the top

pct

number

Percentage in the range [0.0-1.0] determining how full the circle is

thickness

number

Optional. Thickness of the outline in pixels

circle_gradient
render.circle_gradient(position: vector, color_outer: color, color_inner: color, radius: number, start_deg: number, pct: number)

Name	Type	Description
position

vector

Screen position

color_outer

color

Outer color of the circle

color_inner

color

Inner color of the circle

radius

number

Radius of the circle in pixels

start_deg

number

0
 is the right side, 
90
 is the bottom, 
180
 is the left, 
270
 is the top

pct

number

Percentage in the range [0.0-1.0] determining how full the circle is

circle_3d
render.circle_3d(position: vector, color: color, radius: number, start_deg: number, pct: number[, outline: boolean])

Name	Type	Description
position

vector

Screen position

color

color

Color of the circle

radius

number

Radius of the circle in pixels

start_deg

number

0
 is the right side, 
90
 is the bottom, 
180
 is the left, 
270
 is the top

pct

number

Percentage in the range [0.0-1.0] determining how full the circle is

outline

boolean

Optional. Render the circle outline

circle_3d_outline
render.circle_3d_outline(position: vector, color: color, radius: number, start_deg: number, pct: number[, thickness: number])

Name	Type	Description
position

vector

Screen position

color

color

Color of the circle

radius

number

Radius of the circle in pixels

start_deg

number

0
 is the right side, 
90
 is the bottom, 
180
 is the left, 
270
 is the top

pct

number

Percentage in the range [0.0-1.0] determining how full the circle is

thickness

number

Thickness of the outline in pixels

circle_3d_gradient
render.circle_3d_gradient(position: vector, color_outer: color, color_inner: color, radius: number, start_deg: number, pct: number)

Name	Type	Description
position

vector

Screen position

color_outer

color

Outer color of the circle

color_inner

color

Inner color of the circle

radius

number

Radius of the circle in pixels

start_deg

number

0
 is the right side, 
90
 is the bottom, 
180
 is the left, 
270
 is the top

pct

number

Percentage in the range [0.0-1.0] determining how full the circle is

text
📌 Render any text via the :load_font function.

render.text(font: FontObject, position: vector, color: color, flags: string, text:any[, ...])

Name	Type	Description
font

FontObject

Font object or 
1
 for 
Default
 font, 
2
 for 
Small
 font, 
3
 for 
Console
 font, or 
4
 for 
Bold
 font

position

vector

Screen position

color

color

Color of the text

flags

string

c
 for centered text, 
r
 for right-aligned text, 
s
 for DPI scaled text.

nil
 can be specified for normal uncentered text.

text

any

Text that will be drawn

...

Comma-separated vectors to concatenate with text

Draws the specified text.

texture
📌 Create the texture via the :load_image function.

render.texture(texture: ImgObject, position: vector[, size: vector, color: color, mode: string, rounding: number])

Name	Type	Description
texture

ImgObject

Image object

position

vector

Screen position

size

vector

Optional. Size of the texture

color

color

Optional. Color of the texture

mode

string

Optional. 
f
 for fill, 
r
 for repeat

rounding

number

Optional. Roundness of the texture

push_rotation
render.push_rotation(degrees: number)

Name	Type	Description
degrees

number

Rotation degrees (0 - 360)

Applies the rotation for all subsequent elements.

pop_rotation
render.pop_rotation()

Discards an early set rotation.

push_clip_rect
render.push_clip_rect(pos_a: vector, pos_b: vector[, intersect: boolean])

Name	Type	Description
pos_a

vector

Screen position of point A

pos_b

vector

Screen position of point B

intersect

boolean

Optional. Allow intersections with other clip regions

Applies the clip region to the given rectangle for all subsequent elements.

pop_clip_rect
render.pop_clip_rect()

Discards an early set rectangle clipping region.

shadow
render.shadow(pos_a: vector, pos_b: vector, clr: color[, thickness: number, offset: number, rounding: number])

Name	Type	Description
pos_a

vector

Screen position of point A

pos_b

vector

Screen position of point B

clr

color

The color of the shadow

thickness

number

The thickness of the shadow

offset

number

Shadow offset

rounding

number

The rounding of the shadow rectangle

Draws a shadow rectangle.
🧰utils
Functions:
console_exec
utils.console_exec(cmd: string[, ...])

Name	Type	Description
cmd

string

The console command(s) to execute

...

Comma-separated arguments to concatenate with cmd

Executes a console command. Multiple commands can be combined with ';'. Be careful when passing user input (including usernames) to it.

execute_after
utils.execute_after(delay: number, callback: function[, ...])

Name	Type	Description
delay

number

Time in seconds to wait before calling callback

callback

function

The Lua function that will be called after the delay

...

Arguments that will be passed to the callback

Executes the callback after delay seconds, passing the arguments to it.

net_channel
utils.net_channel(): 
NetChannel

Returns the NetChannel struct.

trace_line
Logo
UTIL TraceLine - Valve Developer Community
utils.trace_line(from: vector, to: vector[, skip: entity/table/function, mask: number, type: number]): 
trace

Name	Type	Description
from

vector

Vector to start tracing from

to

vector

Vector to trace to

skip

entity
, 
table
, 
function

Optional. Entity skipping options

mask

number

Optional. Trace mask

type

number

Optional. Trace type [0-3]
0
: Trace everything [Default]
1
: Trace world only
2
: Trace entities only
3
: Trace everything filter props

📌 The skip argument can either be an entity object, a table with entity objects, or a function, like the ShouldHitEntity callback. If you use it as a callback, you can access the entity and contents_mask by adding them to the function arguments. Inside the callback, return true if tracing should not skip the entity, otherwise return false.

Returns a trace struct containing all the information.

trace_hull
utils.trace_hull(from: vector, to: vector, mins: vector, maxs: vector[, skip: entity/table/function, mask: number, type: number]): 
trace

Name	Type	Description
from

vector

Vector to start tracing from

to

vector

Vector to trace to

mins

vector

Hull mins

maxs

vector

Hull maxs

skip

entity
, 
table
, 
function

Optional. Entity skipping options

mask

number

Optional. Trace mask

type

number

Optional. Trace type [0-3]
0
: Trace everything [Default]
1
: Trace world only
2
: Trace entities only
3
: Trace everything filter props

📌 The skip argument can either be an entity object, a table with entity objects, or a function, like the ShouldHitEntity callback. If you use it as a callback, you can access the entity and contents_mask by adding them to the function arguments. Inside the callback, return true if tracing should not skip the entity, otherwise return false.

Returns a trace struct containing all the information.

trace_bullet
utils.trace_bullet(from_entity: entity, from: vector, to: vector[, skip: entity/table/function]): 
number
, 
trace

Name	Type	Description
from_entity

entity

Player whose weapon will be used for this trace

from

vector

Vector to start tracing from

to

vector

Vector to trace to

skip

entity
, 
table
, 
function

Optional. Entity skipping options. If not passed, the skip entity will be from_entity

📌 The skip argument can either be an entity object, a table with entity objects, or a function, like the ShouldHitEntity callback. If you use it as a callback, you can access the entity and contents_mask by adding them to the function arguments. Inside the callback, return true if tracing should not skip the entity, otherwise return false.

Returns the damage, trace arguments.

🔗 struct 
trace
Name	Type	Description
start_pos

vector

Start position

end_pos

vector

Final position

plane

table

Surface normal at impact. Contains 
normal
, 
dist
, 
type
, and 
signbits
 values

fraction

number

Percentage in the range [0.0, 1.0]. How far the trace went before hitting something. 
1.0
 - didn't hit anything

contents

number

Contents on other side of surface hit

disp_flags

number

Displacement flags for marking surfaces with data

all_solid

boolean

Returns 
true
 if the plane is invalid

start_solid

boolean

Returns 
true
 if the initial point was in a solid area

fraction_left_solid

number

Percentage in the range [0.0, 1.0]. How far the trace went before leaving solid. Only valid if we started in solid

surface

table

Surface hit (impact surface). Contains 
name
, 
props
, and 
flags
 values

hitgroup

number

0
 - generic, non-zero is specific body part

physics_bone

number

Physics bone that was hit by the trace

world_surface_index

number

Index of the msurface2_t, if applicable

entity

entity

Entity that was hit by the trace

hitbox

number

Box that was hit by the trace

did_hit

function

Returns 
true
 if there was any kind of impact at all

did_hit_world

function

Returns 
true
 if the entity points at the world entity

did_hit_non_world

function

Returns 
true
 if the trace hit something and it wasn't the world

is_visible

function

Returns 
true
 if the final position is visible

opcode_scan
utils.opcode_scan(module: string, signature: string[, offset: number]): 
userdata

Name	Type	Description
module

string

Module name, in which the signature will be scanned.

signature

string

IDA style signature, the wildcard is "
?
"

offset

number

Optional offset to apply to the pointer address.

Returns a pointer to the specified pattern if it was found. Otherwise returns nil.

create_interface
utils.create_interface(module: string, interface: string): 
userdata

Name	Type	Description
module

string

Module name containing the interface.

interface

string

Interface name.

Returns a pointer to the specified interface if it was found. Otherwise returns nil.

get_netvar_offset
utils.get_netvar_offset(table: string, prop: string): 
number

Name	Type	Description
table

string

Datatable name

prop

string

Prop name

Returns the offset of the specified prop. Can be used to manually navigate to the net prop.

get_vfunc
utils.get_vfunc(index: number, ...): 
function

Name	Type	Description
index

number

Virtual table index of the function.

...

FFI C Type definition.

Creates and returns a wrapper function that calls a virtual table function on the specified index.

get_vfunc
utils.get_vfunc(module: string, interface: string, index: number, ...): 
function

Name	Type	Description
module

string

Module name containing the interface.

interface

string

Interface name.

index

number

Virtual table index of the function.

...

FFI C Type definition.

Creates and returns a wrapper function that calls a virtual table function from the specified interface on the specified index.

random_int
utils.random_int(min: number, max: number): 
number

Name	Type	Description
min

number

Minimum boundary for the random value, included

max

number

Maximum boundary for the random value, included

Returns a random integer value.

random_float
utils.random_float(min: number, max: number): 
number

Name	Type	Description
min

number

Minimum boundary for the random value, included

max

number

Maximum boundary for the random value, included

Returns a random float value.

random_seed
utils.random_seed(seed: number)

Name	Type	Description
seed

number

New random seed value

Sets the new random seed.

🔗 
NetChannel
Access the struct via 
.net_channel
function

FLOW:
OUTGOING
 = 0

FLOW:
INCOMING
 = 1

time
net.time : 
number

Current network time.

time_connected
net.time_connected : 
number

Connection time in seconds.

time_since_last_received
net.time_since_last_received : 
number

Time since last received packet in seconds.

is_loopback
net.is_loopback : 
boolean

Returns 
true
 if server is a loopback (local server).

is_playback
net.is_playback : 
boolean

Returns 
true
 if demo is being played.

is_timing_out
net.is_timing_out : 
boolean

Returns 
true
 if client is timing out.

sequence_nr[
flow
]
net.sequence_nr[0] : 
number
net.sequence_nr[1] : 
number

Last sent sequence number.

latency[
flow
]
net.latency[0] : 
number
net.latency[1] : 
number

Current latency (RTT), more accurate but jittering.

avg_latency[
flow
]
net.avg_latency[0] : 
number
net.avg_latency[1] : 
number

Average latency in seconds.

loss[
flow
]
net.loss[0] : 
number
net.loss[1] : 
number

Percentage in the range [0.0, 1.0] of the current packet loss.

choke[
flow
]
net.choke[0] : 
number
net.choke[1] : 
number

Percentage in the range [0.0, 1.0] of the current packet choke.

packets[
flow
]
net.packets[0] : 
number
net.packets[1] : 
number

Average amount of packets/sec.

data[
flow
]
net.data[0] : 
number
net.data[1] : 
number

Data flow in bytes/sec.

total_packets[
flow
]
net.total_packets[0] : 
number
net.total_packets[1] : 
number

Total amount of packets/sec.

total_data[
flow
]
net.total_data[0] : 
number
net.total_data[1] : 
number

Total data flow in bytes/sec.

:get_server_info
net:get_server_info(): 
table

Returns a table containing 
rate
, 
name
, 
address
, 
frame_time
, and 
deviation
 (or nil on failure).

:is_valid_packet
net:is_valid_packet(flow: number, frame: number): 
number

Name	Type	Description
flow

number

Channel (Flow)

frame

number

Sequence number

Returns 
true
 if the packet is valid.

:get_packet_time
net:get_packet_time(flow: number, frame: number): 
number

Name	Type	Description
flow

number

Channel (Flow)

frame

number

Sequence number

Returns the time when the packet was sent.

:get_packet_bytes
net:get_packet_bytes(flow: number, frame: number, group: number): 
number

Name	Type	Description
flow

number

Channel (Flow)

frame

number

Sequence number

group

number

Group of this packet

Returns the group size of this packet.

:get_packet_response_latency
net:get_packet_response_latency(flow: number, frame: number): 
number
, 
number

Name	Type	Description
flow

number

Channel (Flow)

frame

number

Sequence number

Returns latency_msecs, choke of this packet.
📍vector
Example available!
⚙️
PAGE
Vector
Functions:
:angles
Initialize from
Initialize from [2]
Convert to
vec_object:angles(pitch: number, yaw: number): 
vector

Name	Type	Description
pitch

number

Pitch component of the angle

yaw

number

Yaw component of the angle

Converts the angle into a forward vector overwriting the vector's coordinates. Returns itself.

:ceil
vec_object:ceil(): 
vector

Ceils & overwrites the x, y, and z coordinates of a vector. Returns itself.

:clone
vec_object:clone(): 
vector

Creates and returns a copy of the vector.

:closest_ray_point
vec_object:closest_ray_point(ray_start: vector, ray_end: vector): 
vector

Name	Type	Description
ray_start

vector

Ray start position

ray_end

vector

Ray end position

Returns the vector of the closest point along a ray.

:cross
vec_object:cross(other: vector): 
vector

Name	Type	Description
other

vector

The vector to calculate the cross product with

Returns the cross product of two given vectors.

:dist
vec_object:dist(other: vector): 
number

Name	Type	Description
other

vector

The vector to get the distance to

Returns the Euclidean distance between the two given vectors.

:dist2d
vec_object:dist2d(other: vector): 
number

Name	Type	Description
other

vector

The vector to get the distance to

Returns the 2D distance to another vector.

:dist2dsqr
vec_object:dist2dsqr(other: vector): 
number

Name	Type	Description
other

vector

The vector to get the squared distance to

Returns the squared 2D distance to another vector.

:distsqr
vec_object:distsqr(other: vector): 
number

Name	Type	Description
other

vector

The vector to get the squared distance to

Returns the squared Euclidean distance to another vector.

:dist_to_ray
vec_object:dist_to_ray(ray_start: vector, ray_direction: vector): 
number

Name	Type	Description
ray_start

vector

Ray start position

ray_direction

vector

Ray direction

Returns the distance to a ray.

:dot
vec_object:dot(other: vector): 
number

Name	Type	Description
other

vector

The vector to calculate the dot product with

Returns the dot product of the two given vectors.

:floor
vec_object:floor(): 
vector

Rounds the x, y, and z coordinates of the vector down to the largest integer that is less than or equal. Returns itself.

:in_range
vec_object:in_range(other: vector, range: number): 
boolean

Name	Type	Description
other

vector

The vector to calculate the distance to

range

number

The distance

Returns true if the vector is within the given distance to another vector.

:init
vec_object:init(x: number, y: number, z: number): 
vector

Name	Type	Description
x

number

New X coordinate

y

number

New Y coordinate

z

number

New Z coordinate

Overwrites the vector's coordinates. Returns itself.

:length
vec_object:length(): 
number

Returns the Euclidean length of the vector.

:length2d
vec_object:length2d(): 
number

Returns the length of the vector in two dimensions, without the Z axis.

:length2dsqr
vec_object:length2dsqr(): 
number

Returns the squared length of the vectors x and y value.

:lengthsqr
vec_object:lengthsqr(): 
number

Returns the squared length of the vector.

:lerp
vec_object:lerp(other: vector, weight: number): 
vector

Name	Type	Description
other

vector

The vector to interpolate to

weight

number

A value between 0 and 1 that indicates the weight of other

Returns the linearly interpolated vector between two vectors by the specified weight.

:normalize
vec_object:normalize(): 
number

Normalizes the vector and returns the length of the vector.

:normalized
vec_object:normalized(): 
vector

Returns a vector with the same direction as the specified vector, but with a length of one.

:scale
vec_object:scale(scalar: number): 
vector

Name	Type	Description
scalar

number

The scalar value

Multiplies the vector by the specified scalar.

:scaled
vec_object:scaled(scalar: number): 
vector

Name	Type	Description
scalar

number

The scalar value.

Returns a copy of the vector multiplied by the specified scalar.

:to
vec_object:to(other: vector): 
vector

Name	Type	Description
other

vector

The vector to get the direction to.

Returns the forward vector from itself to another vector.

:to_screen
vec_object:to_screen(): 
vector

Returns a vector containing the coordinates where the specified position vector appears on the screen.

:unpack
vec_object:unpack(): 
number
, 
number
, 
number

Returns the x, y, and z coordinates of the vector. Note that these fields can be accessed by indexing x, y, and z.

:vectors
vec_object:vectors(): 
vector
, 
vector

Returns the right and up vector of a forward vector.

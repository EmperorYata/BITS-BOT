# BITS-BOT
Let's do some coding boys





import discord
import asyncio
import random

client = discord.Client()

insults = ["Fuck you ", "You're tacky and I hate you ",
           "You suck ", "No one likes ",
           "Please cease to exist "]

@client.event
@asyncio.coroutine
def on_ready():
    print('Logged in as')
    print(client.user.name)
    print(client.user.id)
    print('-----')

@client.event
@asyncio.coroutine
def on_message(message):
    print('Recieved: ' + '"' + message.content + '"')
    #   List of possible commands for bot:
    #   !test
    #   !sleep
    #
    #   To add a new command, insert :-
    #   elif message.content.startswith('!<COMMAND_NAME>'):
    #       <COMMAND_LOGIC>
    #

    reply = ""

    if message.content.startswith('!test'):
        reply = test()
    elif message.content.startswith('!sleep'):
        reply = sleep()
    elif message.content.startswith('!insult'):
        insultee = message.author.name
        reply = insult(message.author.name)

    if reply:
        yield from client.send_message(message.channel, reply)


#
#   Functions go here
#

def test():
    return "Testing"

def sleep():
    return "Done Sleeping"

def insult(insultee):
    rand = random.randint(0, len(insults))
    return insults[rand] + insultee

# Replace <BOT TOKEN> with the bots private token
client.run('BOT TOKEN')

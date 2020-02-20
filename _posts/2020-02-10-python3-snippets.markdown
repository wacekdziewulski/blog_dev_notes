---
layout: post
title:  "Python3 snippets"
date:   2020-02-10 12:30:00 +0100
categories: python3
---

Python allows one to write really concise code, which does some pretty nice input processing. It's expressiveness is something that sets it apart from some other languages, especially for some obvious transformations. Here's a few I came up with. Please do comment if You have something interesting or a shorter solution :) I'd love to give it a look. Here's a few of mine.

# Convert array of char codes to string

{% highlight python %}
# input = array of ascii codes for each character
# output = https://devolution.ovh
input = [104, 116, 116, 112, 115, 58, 47, 47, 100, 101, 118, 111, 108, 117, 116, 105, 111, 110, 46, 111, 118, 104]
output = ''.join(map(lambda x : chr(x), input))
print(output)
{% endhighlight %}

# Print a pyramid of incrementing integers

{% highlight python %}
# input:
# 4
# output: 
# 1
# 2 3
# 4 5 6
# 7 8 9 10

n = 4

elements = list(range(1, (n*(n+1))//2+1))

for i in range(1,n+1):
    print(' '.join(map(str, (elements[0:i]))))
    elements = elements[i:]
{% endhighlight %}

# Print text in a spiral

{% highlight python %}
# input:
# 4
# Ilik
# 3Yee
# nhaP
# ohty
# output:
# IlikePython3Yeah
letters = []

n = int(input())
for i in range(n):
    letters.append(list(input()))

max_x = n
max_y = n
steps = 0
x = 0
y = 0

output = []

while(x < max_x and y < max_y):
    if (x < max_x):
        for i in range(x, max_x):
            output.append(letters[y][i])
        y += 1

    if (y < max_y):
        for i in range(y, max_y):
            output.append(letters[i][max_x-1])
        max_x -= 1

    if (max_x > x):
        for i in range(max_x-1, x-1, -1):
            output.append(letters[max_y-1][i])
        max_y -= 1

    if (max_y > y):
        for i in range(max_y-1, y-1, -1):
            output.append(letters[i][x])
        x += 1

print(''.join(output))
{% endhighlight %}

# ROT13

{% highlight python %}
text = input()

def isLetter(character):
    return (ord(character) >= 65 and ord(character) <= 90)

def rot13(character):
    if (isLetter(character)):
        return chr(((ord(character)-65)+13)%26+65)
    else:
        return character;

for character in text:
    print(rot13(character))
{% endhighlight %}

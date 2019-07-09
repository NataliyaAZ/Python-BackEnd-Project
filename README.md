The Back End project was the last project during my time at the Tech Academy.

There are total 4 Live Projects offered by the Academy to their students: Python Project, C# Project, 
Front End and Back End Projects. Students can select either Python or C# for their front-end and back-end projects.
I have selected  Python/Django for the back-end project.

The Django project I worked on with other developers  was a Travel Scrape web application. The Travel Scrape had  
several applications that could be useful for anyone who travels: Weather app, Budget app, Currency Converter app, 
Restaurants app, Slack app, ToDo List app, etc.

My contributions to the projects described below:

#### 1. Created the Restaurant App using Zomato API.
The App allowed travelers to enter the US city and state and returned the top 10 restaurants for that area. The name of the restaurant, the cuisine, average review ranking and average cost for 2 people would be displaied on the screen.

forms.py:
~~~
class CityForm(forms.Form):

    STATES =  (

        ('Alabama', 'AL'),
        ('Alaska', 'AK'),
        ('Arizona', 'AZ'),
        ('Arkansas', 'AR'),
        ('California', 'CA'),
        ('Colorado', 'CO'),
        ('Connecticut', 'CT'),
        ('Delaware', 'DE'),
        ('Florida', 'FL'),
        ('Georgia', 'GA'),
        ('Hawaii', 'HI'),
        ('Idaho', 'ID'),
        ('Indiana', 'IN'),
        ('Iowa', 'IA'),
        ('Kansas', 'KS'),
        ('Kentucky', 'KY'),
        ('Louisiana', 'LA'),
        ('Maine', 'ME'),
        ('Maryland', 'MD'),
        ('Massachusetts', 'MA'),
        ('Minnesota', 'MN'),
        ('Mississippi', 'MS'),
        ('Missouri', 'MO'),
        ('Montana', 'MT'),
        ('Nebraska', 'NE'),
        ('Nevada', 'NV'),
        ('New Hampshire', 'NH'),
        ('New Jersey', 'NJ'),
        ('New Mexico', 'NM'),
        ('New York', 'NY'),
        ('North Carolina', 'NC'),
        ('North Dakota', 'ND'),
        ('Ohio', 'OH'),
        ('Oklahoma', 'OK'),
        ('Oregon', 'OR'),
        ('Oklahoma', 'OK'),
        ('Pennsylvania', 'PA'),
        ('Rhode Island', 'RI'),
        ('South Carolina', 'SC'),
        ('South Dakota', 'SD'),
        ('Tennessee', 'TN'),
        ('Texas', 'TX'),
        ('Utah', 'UT'),
        ('Vermont', 'VT'),
        ('Virginia', 'VA'),
        ('Washington', 'WA'),
        ('West Virginia', 'WV'),
        ('Wisconsin', 'WI'),
        ('Wyoming', 'WY'),
       
    )
        
    
    city = forms.CharField(max_length=100)
    state = forms.ChoiceField(choices=STATES)
~~~
views.py:
~~~
def restaurants(request):

    # Dispalying clear form 
    if request.method == 'GET':
        form = CityForm()
        return render(request, 'Restaurants/restaurants.html', {'form': form})

    # If the user has provided info (method == 'POST')
    else:
        form = CityForm(request.POST)

        #Cleaning the form
        if form.is_valid():
            form = form.cleaned_data

        city = form['city'] #receiving user's city input

        state = form['state'] #receiving user's state input

        cityName = city + ', ' + state #creating the cityName variable in the similar format as in JSON response to compare them later

        #Getting the list of restaurants using Zomato API
        #First, getting city id to be able to search for the restaurants 

        #Calling the API for cities 
        url = "https://developers.zomato.com/api/v2.1/cities?"

        urlParam = {'q': city}

        url = url + urllib.parse.urlencode(urlParam)

        header = {"User-agent": "curl/7.43.0", "Accept": "application/json", "user_key": "932f5d060ad2588af1928fcef440282a"}

        #Receiving json response   and  extracting the correct city's id    
        result = requests.get(url, headers=header)
        
        jsonResult = result.json()
        
        lenJsonDict = len(jsonResult["location_suggestions"])#checking how many elements (cities) came back in the json dictionary
        
        #Searching for the correct city in the correct state (as cities with the same names can be in a different states)
        cityId = 0
        count= 0
        msg =''
        while  count < lenJsonDict:

            location = jsonResult["location_suggestions"][count]

            name =  location["name"]  

            if  name == cityName:   
                cityId = location["id"] #getting the correct city's id
            else:
                msg = 'No restaurants found for the location. Please check if city and state are correct.'#message to display if no city found
            count +=1 

        #Calling the API for restaurants search

        url = "https://developers.zomato.com/api/v2.1/search?"

        urlParam = {'entity_id': cityId, 'entity_type': 'city', 'count': '10'}

        url = url + urllib.parse.urlencode(urlParam)

        header = {"User-agent": "curl/7.43.0", "Accept": "application/json", "user_key": "932f5d060ad2588af1928fcef440282a"}

        #Receiving json response for 10 restaurants 
        result = requests.get(url, headers=header)
        
        jsonResult= result.json()

            
        #Extracting required  data from json response into dictionary
        count = 0
        i = 1
        dictResult = {} 
        while  count < 10:

            restaurants = jsonResult["restaurants"][count]
            restaurant = restaurants["restaurant"]

            restName = restaurant["name"]
            cuisine = restaurant["cuisines"]
            rating = restaurant["user_rating"]["aggregate_rating"]
            costForTwo = restaurant["average_cost_for_two"]

            dictResult[i] ={}
            
            dictResult[i]['Restaurant Name'] =  restName
            dictResult[i]['Cuisine'] = cuisine
            dictResult[i]['Average Rating'] =  rating
            dictResult[i]['Average Cost for Two'] = costForTwo
        
            count +=1
            i +=1

        #Displaying search result  for restaurants
        if cityId != 0:
            return render(request, 'Restaurants/restaurants.html',{'form': CityForm(), 
            'Name1': dictResult[1]['Restaurant Name'],'Cuisine1': dictResult[1]['Cuisine'], 'Rating1':  dictResult[1]['Average Rating'],'Cost1': dictResult[1]['Average Cost for Two'],
            'Name2': dictResult[2]['Restaurant Name'],'Cuisine2': dictResult[2]['Cuisine'], 'Rating2':  dictResult[2]['Average Rating'],'Cost2': dictResult[2]['Average Cost for Two'],
            'Name3': dictResult[3]['Restaurant Name'],'Cuisine3': dictResult[3]['Cuisine'], 'Rating3':  dictResult[3]['Average Rating'],'Cost3': dictResult[3]['Average Cost for Two'],
            'Name4': dictResult[4]['Restaurant Name'],'Cuisine4': dictResult[4]['Cuisine'], 'Rating4':  dictResult[4]['Average Rating'],'Cost4': dictResult[4]['Average Cost for Two'],
            'Name5': dictResult[5]['Restaurant Name'],'Cuisine5': dictResult[5]['Cuisine'], 'Rating5':  dictResult[5]['Average Rating'],'Cost5': dictResult[5]['Average Cost for Two'],
            'Name6': dictResult[6]['Restaurant Name'],'Cuisine6': dictResult[6]['Cuisine'], 'Rating6':  dictResult[6]['Average Rating'],'Cost6': dictResult[6]['Average Cost for Two'],
            'Name7': dictResult[7]['Restaurant Name'],'Cuisine7': dictResult[7]['Cuisine'], 'Rating7':  dictResult[7]['Average Rating'],'Cost7': dictResult[7]['Average Cost for Two'],
            'Name8': dictResult[8]['Restaurant Name'],'Cuisine8': dictResult[8]['Cuisine'], 'Rating8':  dictResult[8]['Average Rating'],'Cost8': dictResult[8]['Average Cost for Two'],
            'Name9': dictResult[9]['Restaurant Name'],'Cuisine9': dictResult[9]['Cuisine'], 'Rating9':  dictResult[9]['Average Rating'],'Cost9': dictResult[9]['Average Cost for Two'],
            'Name10': dictResult[10]['Restaurant Name'],'Cuisine10': dictResult[10]['Cuisine'], 'Rating10':  dictResult[10]['Average Rating'],'Cost10': dictResult[10]['Average Cost for Two'],
            })
       
        return render(request, 'Restaurants/restaurants.html',{'form': CityForm(), 'msg': msg})
~~~            
#### 2.Slack App.
Created the Slack App (using the Slack API) that allowed  travelers to send message from the TravelScrape website to their
Slack Workspaces.

.html:
~~~
% extends "base.html" %}

{% block content %}
{% include "nav.html" %}

<div class="container" style="background-color: white; width:30%; height:40%;">

    <h6 class='text-center'>{{message}} </h6>
    <!-- Slack button -->
  
    {% if authFlag %}
        <a href="https://slack.com/oauth/authorize?client_id={{client_id}}&scope=bot" name="slacklink">
            <img alt="Add to Slack" height="40" width="139" src="https://platform.slack-edge.com/img/add_to_slack.png" 
            srcset="https://platform.slack-edge.com/img/add_to_slack.png 1x, https://platform.slack-edge.com/img/add_to_slack@2x.png 2x">
        </a>
    {% endif %}

    <form method = "POST">
        {% csrf_token %}
        {{form}}
        {% if message == 'Please fill out the form and send your message to Slack' %}
            <button name="slackMsg">Send message to Slack</button>
        {% endif %}
    </form>
    
    {% if message == 'The Channel you entered does not exist. Please try again.' or message == 'Your message was sent to Slack'  %}
        <a href="{% url 'slack' %}" name="">Back to authorization page</a>
    
    {% endif %}
</div>

{% endblock content %}
~~~
forms.py:
~~~
class SlackMsgForm(forms.Form):
    slack_username = forms.CharField(max_length=100,widget=forms.TextInput(attrs={'placeholder': 'Your Username on Slack'}))
    channel = forms.CharField(max_length=100, 
              widget=forms.TextInput(attrs={'placeholder': 'Slack channel: general, random,etc'}))
    message = forms.CharField( max_length=2000,
              widget=forms.Textarea())
    bot_token = forms.CharField(widget = forms.HiddenInput())
~~~
views.py:
~~~
def slack(request):
    client_id = settings.SLACK_CLIENT_ID
    
    #Getting the code from user's authorization/installation approval
    code = request.GET.get('code','')
    message = 'Please press the button to authorize the application'
    authFlag = True 
    #If authorization done and the code received
    if code != '': 

        #Posting message to Slack after the form was filled out
        if request.method =='POST':
            form = SlackMsgForm(request.POST)

            #Cleaning the form
            if form.is_valid():
                form = form.cleaned_data
            
            bot_token = form['bot_token']
            username = form['slack_username']#The message will be posted to Slack under this username(User can enter any username they wish)
            channel = '#' + form['channel']
            message = form['message']

            params = {
                        'token': bot_token,
                                            
                        'channel': channel,
                
                        'text': message,

                        'as_user': 'false',

                        'username': username
            }
            
            url = "http://slack.com/api/chat.postMessage?"
            url = url + urllib.parse.urlencode(params)
            json_response = requests.get(url)
            data = json_response.json()
            confirmation = data['ok']
            if confirmation is True:
                message = 'Your message was sent to Slack'
            else:
                message = 'The Channel you entered does not exist. Please try again.'#Only incorrect channel can be a probelm for not sending the msg to Slack
            confirmFlag = True
            return render(request, 'SlackApp/slack.html',{'message': message,'confirmFlag': confirmFlag} )
        else:

            #Preparing parameters for oauth.access method 
            params = {  'code': code,
                                            
                        'client_id': settings.SLACK_CLIENT_ID,
                
                        'client_secret': settings.SLACK_CLIENT_SECRET
            }

            #Calling Slack API with oauth.access method:
            url = 'https://slack.com/api/oauth.access?'
            url = url + urllib.parse.urlencode(params)
            json_response = requests.get(url)
            data = json_response.json()

            #Getting bot access token - the required parameter for posting  message to Slack
            bot_token=data['bot']['bot_access_token']
            form = SlackMsgForm(initial={'bot_token':bot_token})#Sending bot token into the form's hidden field (to save it)
            message = 'Please fill out the form and send your message to Slack'
            formFlag = True
                    
            return render(request, 'SlackApp/slack.html',{'message': message,'form': form, 'formFlag': formFlag})
    
    return render(request, 'SlackApp/slack.html',{'client_id': client_id, 'message': message, 'authFlag': authFlag})
~~~
#### 3.ToDoList Integration.
ToDo List App allowed travelers to add tasks into 4 different lists: today's task, this week,
this month and spare time tasks. The lists were not connected to each other. There was All Task page that needed to show 
all tasks by timeframe category. In addition, the user could add a new task from the All Tasks page and assign it to any 
timeframe category.


.html:
~~~
{% extends 'ToDo_base.html' %}

{% block add_task %}
<form class="form-inline my-2 my-lg-0" method="POST">
{% csrf_token %}
<input class="form-control mr-sm-2" type="search" placeholder="Add Item" aria-label="Search" name="item">
<select name="timeframe" class="form-control mr-sm-2" >
    <option selected="selected" value="select">Select Timeframe</option>
    <option value="today" name="today">Today</option>
    <option value="thisweek" name="thisweek">This Week</option>
    <option value="thismonth" name="thismonth">This Month</option>
    <option value="sparetime" name="sparetime">Spare Time Tasks</option>
  </select>
<button class="btn btn-light btn-outline-secondary my-2 my-sm-0" type="submit">Add</button>
</form> 
{% endblock %}

{% block content %}
<br>
<h1>ALL TASKS</h1>
<br>
    {% if messages %}
    {% for message in messages %}
        <div class="alert alert-warning alert-dismissible" role="alert">
            <button class="close" data-dismiss="alert">
                <small><sup>x</sup></small>
            </button>
            {{ message }}
        </div>
    {% endfor %}

{% endif %}

<h3>Today</h3>
{% if today_items %}
    <table class="table table-bordered table-white">
    {% for things in today_items %}
        {% if things.completed %}
            <tr class="table-secondary">
                <td class="striker"><h4>{{ things.item }}</h4></td>
                <td><center><a href="{% url 'uncross_today' things.id %}"><h4>Uncross</h4></a></center></td>
                <td><center><a href="{% url 'delete_today' things.id %}"><h4>Delete</h4></a></center></td>
            </tr>
        {% else %}  
            <tr>
                <td><h4>{{ things.item }}</h4></td>
                <td><center><a href="{% url 'cross_off_today' things.id %}"><h4>Cross Off</h4></a></center></td>
                <td><center><a href="{% url 'delete_today' things.id %}"><h4>Delete</h4></a></center></td>
            </tr>
        {% endif %}
    {% endfor %}
    </table>
{% endif %}

<h3>This Week</h3>
{% if thisweek_items %}
    <table class="table table-bordered table-white">
    {% for things in thisweek_items %}
        {% if things.completed %}
            <tr class="table-secondary">
                <td class="striker"><h4>{{ things.item }}</h4></td>
                <td><center><a href="{% url 'uncross_thisweek' things.id %}"><h4>Uncross</h4></a></center></td>
                <td><center><a href="{% url 'delete_thisweek' things.id %}"><h4>Delete</h4></a></center></td>
            </tr>
        {% else %}  
            <tr>
                <td><h4>{{ things.item }}</h4></td>
                <td><center><a href="{% url 'cross_off_thisweek' things.id %}"><h4>Cross Off</h4></a></center></td>
                <td><center><a href="{% url 'delete_thisweek' things.id %}"><h4>Delete</h4></a></center></td>
            </tr>
        {% endif %}
    {% endfor %}
    </table>
{% endif %}

<h3>This Month</h3>
{% if thismonth_items %}
    <table class="table table-bordered table-white">
    {% for things in thismonth_items %}
        {% if things.completed %}
            <tr class="table-secondary">
                <td class="striker"><h4>{{ things.item }}</h4></td>
                <td><center><a href="{% url 'uncross_thismonth' things.id %}"><h4>Uncross</h4></a></center></td>
                <td><center><a href="{% url 'delete_thismonth' things.id %}"><h4>Delete</h4></a></center></td>
            </tr>
        {% else %}  
            <tr>
                <td><h4>{{ things.item }}</h4></td>
                <td><center><a href="{% url 'cross_off_thismonth' things.id %}"><h4>Cross Off</h4></a></center></td>
                <td><center><a href="{% url 'delete_thismonth' things.id %}"><h4>Delete</h4></a></center></td>
            </tr>
        {% endif %}
    {% endfor %}
    </table>
{% endif %}

<h3>Spare Time Tasks</h3>
{% if sparetime_items %}
    <table class="table table-bordered table-white">
    {% for things in sparetime_items %}
        {% if things.completed %}
            <tr class="table-secondary">
                <td class="striker"><h4>{{ things.item }}</h4></td>
                <td><center><a href="{% url 'uncross_spare_time' things.id %}"><h4>Uncross</h4></a></center></td>
                <td><center><a href="{% url 'delete_spare_time' things.id %}"><h4>Delete</h4></a></center></td>
            </tr>
        {% else %}  
            <tr>
                <td><h4>{{ things.item }}</h4></td>
                <td><center><a href="{% url 'cross_off_spare_time' things.id %}"><h4>Cross Off</h4></a></center></td>
                <td><center><a href="{% url 'delete_spare_time' things.id %}"><h4>Delete</h4></a></center></td>
            </tr>
        {% endif %}
    {% endfor %}
    </table>
{% endif %}

{% endblock %}
~~~            

models.py(created before by other developer in the team):
~~~
class List_today(models.Model):
    item = models.CharField(max_length=200)
    completed = models.BooleanField(default=False)

class List_thisweek(models.Model):
    item = models.CharField(max_length=200)
    completed = models.BooleanField(default=False)

class List_thismonth(models.Model):
    item = models.CharField(max_length=200)
    completed = models.BooleanField(default=False)

class List_spare_time(models.Model):
    item = models.CharField(max_length=200)
    completed = models.BooleanField(default=False)

    def __str__(self):
        return self.item + ' | ' + str(self.completed)
~~~
views.py:
~~~
def ToDoListApp(request):   
    if request.method == 'POST':
        if request.POST.get('item') and request.POST.get('timeframe'):
            post = list()
            timeframeOption = request.POST.get('timeframe')
            if  timeframeOption == 'today':
                post = List_today()
            elif  timeframeOption == 'thisweek':
                post = List_thisweek()
            elif timeframeOption == 'thismonth':
                post = List_thismonth()
            elif timeframeOption == 'sparetime':
                post = List_spare_time()
            else:
                today_items = List_today.objects.all
                thisweek_items = List_thisweek.objects.all
                thismonth_items = List_thismonth.objects.all
                sparetime_items = List_spare_time.objects.all     
                return render(request, 'ToDoListApp.html', {'today_items': today_items, 'thisweek_items': thisweek_items, 
                        'thismonth_items': thismonth_items, 'sparetime_items':sparetime_items})
                   
            post.item = request.POST.get('item')    
            post.save()
            today_items = List_today.objects.all
            thisweek_items = List_thisweek.objects.all
            thismonth_items = List_thismonth.objects.all
            sparetime_items = List_spare_time.objects.all     
            return render(request, 'ToDoListApp.html', {'today_items': today_items, 'thisweek_items': thisweek_items, 
                        'thismonth_items': thismonth_items, 'sparetime_items':sparetime_items})
        else: 
            today_items = List_today.objects.all
            thisweek_items = List_thisweek.objects.all
            thismonth_items = List_thismonth.objects.all        
            sparetime_items = List_spare_time.objects.all    
            return render(request, 'ToDoListApp.html', {'today_items': today_items, 'thisweek_items': thisweek_items, 
                        'thismonth_items': thismonth_items, 'sparetime_items':sparetime_items})       
    
~~~



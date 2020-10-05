Project Summery
===============

This is a project to create a profile information

Goal Achieved
--------------

Goal 1 - Familer with one to one relation.

Goal 2 - familer with reverse relation.

Code
----
Write the feilds in your or file.

*forms.py*

::

   class ProfileForm(forms.Form):
       first_name=forms.CharField(max_length=200,initial = "first name")
       last_name=forms.CharField(max_length=200 ,initial = "last name")
       mobile_number=forms.IntegerField(initial = 242342342)
       address=forms.CharField(max_length=500 ,initial = "your address")
       country=forms.CharField(max_length=200 ,initial = "india")


*for creating models write models.py files*

::


    class Profile(models.Model):
        user=models.OneToOneField(User,related_name='profile',on_delete=models.CASCADE)
        # profile_pic=models.FileField()
        # first_name=models.CharField(max_length=200,blank=True)
        # last_name=models.CharField(max_length=200,blank=True)
        # birth_date=models.DateField(null=True,blank=True)
        mobile_number=models.IntegerField(default=0)
        address=models.TextField(max_length=500,blank=True)
        country=models.TextField(max_length=True,blank=True)

        def __str__(self):
            return f"{self.id}"
        def testy(self):
            print("hello")

    @receiver(post_save,sender=User)
    def create_user_profile(sender,instance,created,**kwargs):
        # print("signal triggered")
        # print(instance)
        # print(kwargs)
        if created:
            Profile.objects.create(user=instance)

    @receiver(post_save,sender=User)
    def save_user_profile(sender,instance,**kwargs):
        instance.profile.save()





in views.py file include the logic for displaying
for index view

*views.py*

::

    def index(request):
        # if request.user.is_authenticated:
        print(request.user)
        latest_question_list = Question.objects.order_by('-pub_date')[:5]
        template = loader.get_template('polls/index.html')
        profileform =ProfileForm(request.POST or None)
        context = {
            'latest_question_list': latest_question_list,
            'form': profileform,

        }
    return HttpResponse(template.render(context, request))


*for calling the html templates write the following function*

::

    def profile(request):
        template = loader.get_template('polls/profile.html')
        return HttpResponse(template.render({}, request))

*in templates write the following code*

::

    <!DOCTYPE html>
    <html lang="en">

    <!-- CSS only -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous">

    <!-- JS, Popper.js, and jQuery -->
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.1/dist/umd/popper.min.js" integrity="sha384-9/reFTGAW83EW2RDu2S0VKaIzap3H66lZH81PoYlFhbGU+6BZp6G7niu735Sk7lN" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js" integrity="sha384-B4gt1jrGC7Jh4AgTPSdUtOBvfO8shuf57BaghqFfPlYxofvL8/KUEfYiJOMMV+rV" crossorigin="anonymous"></script>


    <body>
    <a href="/logout/" >
        <button  target="_blank" style="float:right;" >Logout</button>
    </a>
    <a href="/setpassword/" >
        <button  target="_blank" style="float:right;" >Reset Password</button>
    </a>
    <br>
    <span id="info" style="float:right" >
    {% if user.is_authenticated %}
        <p><b>User -</b> {{ user.get_username}}. <b>Mail - </b>{{ user.email}}.</p>
        <p><b>first name -</b> {{ user.first_name}}. <b>last name - </b>{{ user.last_name}}.</p>
        <p><b>mobile_number -</b> {{ user.profile.mobile_number}}. <b>address- </b>{{ user.profile.address}}.</p>
    {% endif %}
    </span>
    {% if latest_question_list %}
        <ul>
        {% for question in latest_question_list %}
            <li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
        {% endfor %}
        </ul>
    {% else %}
        <p>No polls are available.</p>
    {% endif %}


    <div style="float:right" >

    <form action="/profile-handle/" method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <input type="submit" value="Edit">
    </form>

        <!--<p><b>First Name -</b> {{form.first_name}}.</p>
        <p><b>Last Name -</b> {{form.last_name}}.</p>
        <p><b>Date of Birth -</b> {{form.birth_date}}.</p>
        <p><b>Mobile Number -</b> {{form.mobile_number}}.</p>
        <p> <b>Address - </b>{{ user.address}}.</p>
        <p><b>Country -</b> {{form.country}}.</p>-->

    </div>
    </body>
    </html>

*call the templates using*

::

    def profile(request):
        template = loader.get_template('polls/profile.html')
        return HttpResponse(template.render({}, request))

*take all data from th form and save in user profile module in view.py*

::

    def profile_handle(request):
        print(request.user)
        form = ProfileForm(request.POST)
        user = request.user
        user.profile.testy()
        if form.is_valid():

            mobile_number = form.cleaned_data['mobile_number']
            address = form.cleaned_data['address']
            country = form.cleaned_data['country']
            user.first_name = form.cleaned_data['first_name']
            user.last_name = form.cleaned_data['last_name']
            user.save()
            profile = user.profile
            profile.mobile_number = mobile_number
            profile.address = address
            profile.save()
            return redirect("/profile")
        else:
            print(form.errors)
            messages.error(request, '  Not a valid ')
            return redirect('/')

*urls for poles-urls.py*

::

    from.import views
    app_name='polls'
    urlpatterns=[
        path('', views.index, name='index'),
        path('login/', views.login_page, name='login'),
        path('logout/', views.logout_page, name='logout'),
        path('setpassword/', views.set_password, name='set_password'),
        path('profile-handle/', views.profile_handle, name='profileview'),
        path('profile/', views.profile, name='profile'),
        path('login-handle/', views.login_handle, name='index_handle'),
        path('password-handle/', views.password_handle, name='password_handle'),
        path('polls/<int:pk>/',views.DetailView.as_view(),name='detail'),
        path('polls/<int:pk>/results/', views.ResultsView.as_view(), name='results'),
        path('polls/<int:question_id>/vote/', views.vote, name='vote'),

]

*setting file add the app name*

::


    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'polls',
    ]

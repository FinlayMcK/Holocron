> [!important]- Goal



# Referees

> [!tip] This was the extension goal for a previous week. If you have completed this already, then skip this section.

## Database & Model Changes

Create the table in the database, following the process from previous modifications:

```sql
CREATE TABLE resume_referees(  
    id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    contact_number TEXT NOT NULL,
    email_address TEXT NOT NULL,
    userID INTEGER NOT NULL
);
```

Update `models.py` to create a new class to match the new table:

![resumeFinalRefereeModel](/WebDev/_shared/Projects/ANH/images/resumeFinalRefereeModel.png)

```python
class ResumeReferees(db.Model):
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    name = db.Column(db.String(255))
    contact_number = db.Column(db.String(255))
    email_address = db.Column(db.String(255))
    userID = db.Column(db.Integer)
```

## `forms.py`

Open `forms.py`. Due to changes required further in the code, you will need to:
- Change the variable for the SubmitField for `ResumeForm` to `submit1`, and 
- Create the new RefereeForm class. Notice that the SubmitField is named `submit2`.

![resumeFinalForms](/WebDev/_shared/Projects/ANH/images/resumeFinalForms.png)

```python
class RefereeForm(FlaskForm):
    refereeName = StringField("Referee's Name", validators=[DataRequired()])
    refereeContactNumber = StringField("Referee's Contact Number", validators=[DataRequired()])
    refereeEmailAddress = StringField("Referee's Email Address", validators=[DataRequired()])
    submit2 = SubmitField("Add Referee")
```


## `app.py`

### Imports

First, add the newly created classes to the import statements near the top of the file.

![resumeFinalImport](/WebDev/_shared/Projects/ANH/images/resumeFinalImports.png)

### `resumeBuild()`

Several changes need to be made to this function for the new referee functionality to be included. The first step is renaming `form` to `formResume` to distinguish it from the other form (to be added).

Right-click on `form` and choose **Rename Symbol**. Change the name to `formResume` and hit enter. 

![resumeFinalRename](/WebDev/_shared/Projects/ANH/images/resumeFinalRename.png)

Create a new variable for `RefereeForm`.

![resumeFinalRefereeForm](/WebDev/_shared/Projects/ANH/images/resumeFinalRefereeForm.png)


```python
formReferee = RefereeForm()
```

Update the remainder of the function to check which form was submitted (using which button) and update the relevant database. The `renderTemplate()` function call will also need to be updated to send both forms to the `resumeBuild.html` template.

![resumeFinalValidateSubmit](/WebDev/_shared/Projects/ANH/images/resumeFinalValidateSubmit.png)

```python
if formResume.submit1.data and formResume.validate:
	new_resume = ResumeExperience(
		userID=current_user.id, 
		experienceTitle=request.form['experienceTitle'], 
		experienceTime=request.form['experienceTime'], 
		experienceLocation=request.form['experienceLocation'], 
		experienceDescription=request.form['experienceDescription'], 
		experienceWork=False if request.form.get('experienceWork') == None else True)
	db.session.add(new_resume)
	db.session.commit()

if formReferee.submit2.data and formReferee:
	new_referee = ResumeReferees(
		userID=current_user.id, 
		name=request.form['refereeName'], 
		contact_number=request.form['refereeContactNumber'], 
		email_address=request.form['refereeEmailAddress'])
	db.session.add(new_referee)
	db.session.commit()

return redirect(url_for("resumeBuild"))
```


### `resumeDisplay()`
Update `resumeDisplay()` to load the referees for the user. This process is done in the same manner as loading the experience from the database, just from the new table.

You'll then need to add the referee data to send to the template.

![resumeFinalDisplay](/WebDev/_shared/Projects/ANH/images/resumeFinalDisplay.png)

```python
userReferees = ResumeReferees.query.filter_by(userID=current_user.id).all()
```

## `resumeBuild.html`

Open `resumeBuild.html` and update to reflect the changes made, to accomodate adding Experience and Referees.

![resumeFinalFormUpdate](/WebDev/_shared/Projects/ANH/images/resumeFinalFormUpdate.png)

Add the code for the Referee form.

> [!important] Pay attention to the `</div>` tags to ensure the page will be laid out correctly.

![resumeFinalFormNew](/WebDev/_shared/Projects/ANH/images/resumeFinalFormNew.png)

```html
<div class="row">
	<h3>New Referee</h3>
</div>
<div class="row">
	<form method="POST" action="/resumeBuild">
		<div class="mb-3">
			<label for="title" class="form-label">Referee Name</label>
			<input type="text" class="form-control" id="refereeName" name="refereeName" placeholder="Enter Name">
		</div>
		<div class="mb-3">
			<label for="description" class="form-label">Referee Contact Number</label>
			<input type="text" class="form-control" id="refereeContactNumber" name="refereeContactNumber"
				placeholder="Enter Contact Number">
		</div>
		<div class="mb-3">
			<label for="description" class="form-label">Referee Email Address</label>
			<input type="text" class="form-control" id="refereeEmailAddress" name="refereeEmailAddress"
				placeholder="Enter Email Address">
		</div>
		<p>{{ formReferee.submit2() }}</p>
</div>
```

## `resumeDisplay.html`

The final step is to code the resume to display the referees. Open `resumeDisplay.html`.

Add the code to iterate over any referees the user has and display them.

![resumeFinalRefereesDisplay](/WebDev/_shared/Projects/ANH/images/resumeFinalRefereesDisplay.png)

```html
<h2>Referees</h2>

<div class="container-fluid">
  {% for referee in userReferees %}


  <div class="row">
    <div class="col-sm-4">
      <h6>{{referee.name}}</h6>
    </div>
    <div class="col-sm-4">{{referee.email_address}}</div>
    <div class="col-sm-4">{{referee.contact_number}}</div>
  </div>
  {% endfor %}

</div>
```

> [!tip] Currently, the referees are displayed in a standard bootstrap grid. Your challenge is to display the information better, such as how the experience information was displayed.


![[commonBlocks#Commit & Push]]





# Skills
> [!tip] Some resumes include a skills section for people to rate their knowledge on certain topics.




# Printing

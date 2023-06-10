# todoapp
#Todo app created in flask
# I created a new directory named "templates" to store the requires html files
from flask import Flask, render_template, request, url_for,redirect

app= Flask(__name__, template_folder="templates")
todos=[{"task": "Sample Todo", "done": False}]
@app.route("/")
def index():
    return render_template("index.html", todos=todos)

# Use the decorators appropriately
@app.route("/add", methods=["POST"])
def add():
    todo = request.form["todo"]
    todos.append({"task" : todo, "done" : False})
    return redirect(url_for("index"))

@app.route("/edit/<int:index>", methods=["GET", "POST"])
def edit(index):
    todo = todos[index]
    if request.method == "POST":
        todo["task"] = request.form["todo"]
        return redirect(url_for("index"))
    else:
        return render_template("edit.html", todo=todo, index=index)

@app.route("/check/<int:index>")
def check(index):
    todos[index]["done"] = not todos[index]["done"]
    return redirect(url_for("index"))

@app.route("/delete/<int:index>")
def delete(index):
    del todos[index]
    return redirect(url_for("index"))
if __name__ == "__main__":
    app.run(debug=True)
    
    
# I have created the following html files
# index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Todo App</title>
</head>
<body>
  <h1>Todos</h1>
  <ul>
      {% for todo in todos %}
         <li>
             <input type="checkbox" name="done" {% if todo['done'] %} checked {%endif%} disabled onchange="this.form.submit()">
             <span {% if todo['done'] %} style="text-decoration: line-through" {%endif%}>{{ todo['task'] }}</span>
             <a href="{{ url_for('check', index=loop.index0) }}">Check</a>
             <a href="{{ url_for('edit', index=loop.index0) }}">Edit</a>
             <a href="{{ url_for('delete', index=loop.index0) }}">Delete</a>
         </li>
      {% endfor %}
  </ul>
    <form action="{{ url_for('add') }}" method="post">
        <input type="text" name="todo">
        <button type="submit">Add Todo</button>
    </form>
</body>
</html>

# edit.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="{{ url_for('edit', index=index) }}" method="post">
        <input type="text" name="todo" value="{{ todo['task'] }}">
        <button type="submit">Save</button>
    </form>
</body>
</html>

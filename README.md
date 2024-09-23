# CURSO: APRENDE BLAZOR

# LECCIÓN 42: Funciones del ciclo de vida del componente (Blazor lifecycle methods)

El ciclo de vida (Blazor lifecycle methods) de la aplicación web Blazor se refiere a las diversas fases por las que pasa un componente Blazor durante su existencia. 

1. Abrir la aplicación con Visual Studio 2022 o VSCode

2. OnInitialized y OnInitializedAsync

Propósito: este método es invocado cuando un componente es inicializado por primera vez

Caso de uso: incluye la lógica de inicialización estableciendo los valores por defecto o realizando llamadas HTTP

```razor
@page "/initialize-example"

<h3>OnInitialized Example</h3>

<p>Message: @message</p>

@code {
    private string message = "Initializing...";

    protected override void OnInitialized()
    {
        message = "Component Initialized!";
    }

    // If you want to perform asynchronous operations, use OnInitializedAsync
    // protected override async Task OnInitializedAsync()
    // {
    //     await Task.Delay(1000); // Simulate delay
    //     message = "Initialized after async operation!";
    // }
}
```

3. OnParametersSet y OnParametersSetAsync

Propósito: este método es invocado cuando el componente recibe un nuevos parámetros 

Esto ocurre durante el renderizado inicial y cada vez que un parámetro del componente cambia

Caso de uso: cuando cambiamos la ruta o un componente padre envía un parámetro nuevo, este método OnParameterSet es invocado

```razor
@page "/parameters-example/{Name}"

<h3>OnParametersSet Example</h3>
</ br>
<p>Welcome, @Name!</p>
</ br>
<p>Render count: @message</p>

@code {

    [Parameter]
    public string Name { get; set; } = "Default";

    private string message = "";

    protected override void OnParametersSet()
    {
        message = $"Parameters set: {Name}";
    }

    // For async version
    // protected override async Task OnParametersSetAsync()
    // {
    //     await Task.Delay(500); // Simulate delay
    //     message = "Parameters set with async logic";
    // }
}
```

4. OnAfterRender y OnAfterRenderAsync

Propósito: este método es invocado cada vez que un componente es renderizado. La llamada se realiza después de que el componente se haya renderizado

Caso de uso: este ejemplo This example tracks how many times the component renders and prints messages based on whether it's the first or subsequent render.

```razor
@page "/after-render-example"

<h3>OnAfterRender Example</h3>
</ br>
<p>Render count: @renderCount</p>
</ br>
<p>Render count: @message</p>

@code {
    private int renderCount = 0;
    private string message = "";

    protected override void OnAfterRender(bool firstRender)
    {
        renderCount++;

        if (firstRender)
        {
            message = "Component rendered for the first time";
        }
        else
        {
            message = "Component re-rendered";
        }
    }

    // Asynchronous version
    // protected override async Task OnAfterRenderAsync(bool firstRender)
    // {
    //     if (firstRender)
    //     {
    //         await Task.Delay(500); // Simulate some async operation
    //         Console.WriteLine("After render async logic executed.");
    //     }
    // }
}
```

5. ShouldRender

Propósito: determina si un componente debe renderizarse otra vez

Caso de uso: mejora el rendimiento evitando renderizados innecesarios. 

En este ejemplo, alternar preventRender controla si el componente debe volver a renderizarse cuando se hace clic en el botón

```razor
@page "/should-render-example"

<h3>ShouldRender Example</h3>
</ br>
<p>Message: @message</p>
</ br>
<button @onclick="ChangeMessage">Change Message</button>

@code {
    private string message = "Hello World";
    private bool preventRender = false;

    private void ChangeMessage()
    {
        message = "Message changed!";
        preventRender = !preventRender; // Toggle the rendering prevention
    }

    protected override bool ShouldRender()
    {
        // If preventRender is true, component will not re-render
        return !preventRender;
    }
}
```

6. StateHasChanged

En este ejemplo se demuestra el uso del método StateHasChanged. Este método es utilizado en Blazor para forzar el renderizado del interfaz del usuario UI cuando los datos se han modificado. 

```razor
@page "/"

<h3>Blazor StateHasChanged Example</h3>

<p>Counter Value: @counter</p>

<button class="btn btn-primary" @onclick="IncrementCounter">Increment Counter</button>
<button class="btn btn-warning" @onclick="ForceStateChange">Force StateHasChanged</button>

@code {
    private int counter = 0;

    private void IncrementCounter()
    {
        counter++;
        // The UI will not automatically update here
        // unless StateHasChanged is explicitly called or
        // an event triggers a re-render.
    }

    private void ForceStateChange()
    {
        // Manually call StateHasChanged to re-render the UI
        StateHasChanged();
    }
}
```

Otro ejemplo de renderizado del UI

```razor
@page "/taskmanager"

<h3>Task Manager</h3>

<div>
    <input type="text" placeholder="Task Title" @bind="newTaskTitle" />
    <input type="text" placeholder="Task Description" @bind="newTaskDescription" />
    <button class="btn btn-primary" @onclick="AddTask">Add Task</button>
</div>

<h4>Task List</h4>
@if (tasks.Count == 0)
{
    <p>No tasks available.</p>
}
else
{
    <ul>
        @foreach (var task in tasks)
        {
            <li>
                <div>
                    <strong>@task.Title</strong> - @task.Description
                    @if (task.IsCompleted)
                    {
                        <span class="text-success">(Completed)</span>
                    }
                    else
                    {
                        <button class="btn btn-success" @onclick="() => MarkTaskAsCompleted(task)">Mark as Completed</button>
                    }
                </div>
            </li>
        }
    </ul>
}

@code {
    private List<TaskItem> tasks = new List<TaskItem>();
    private string newTaskTitle;
    private string newTaskDescription;

    private void AddTask()
    {
        if (!string.IsNullOrEmpty(newTaskTitle) && !string.IsNullOrEmpty(newTaskDescription))
        {
            tasks.Add(new TaskItem
                {
                    Title = newTaskTitle,
                    Description = newTaskDescription,
                    IsCompleted = false
                });

            // Clear input fields
            newTaskTitle = string.Empty;
            newTaskDescription = string.Empty;

            // Trigger UI update after adding a task
            StateHasChanged();
        }
    }

    private void MarkTaskAsCompleted(TaskItem task)
    {
        task.IsCompleted = true;

        // Update UI to reflect the task as completed
        StateHasChanged();
    }

    public class TaskItem
    {
        public string? Title { get; set; }
        public string? Description { get; set; }
        public bool IsCompleted { get; set; }
    }
}
```

7. Dispose

Propósito: limpia los recursos cuando el componente es destruido

Caso de uso: liberar recursos o suscripciones no administrados. Este ejemplo utiliza el método Dispose para limpiar un temporizador cuando se destruye el componente

Puede implementar IDisposable en su componente para este propósito

```razor
@page "/dispose-example"

<h3>Dispose Example</h3>
</ br>
<p>@message</>


@code {
    private Timer _timer;
    private string message = "";

    protected override void OnInitialized()
    {
        _timer = new Timer(TimerCallback, null, 0, 1000);
    }

    private void TimerCallback(object state)
    {
        InvokeAsync(() =>
        {
            message = "Timer tick";
        });
    }

    public void Dispose()
    {
        _timer?.Dispose();
        message = "Component disposed and timer stopped.";
    }
}
```

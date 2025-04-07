<script lang="ts">
    import '../app.css';
    import { Button, Input, Card, Heading, Badge } from 'flowbite-svelte';
    import { Plus, Trash } from '@lucide/svelte';
	import { onMount } from 'svelte';
    import { slide } from 'svelte/transition';
    let inputValue = $state("");
    let todos = $state<string[]>([]);

    // Load todos from localStorage on initialization
    onMount(() => {
        const storedTodos = localStorage.getItem("todos");
        if (storedTodos) {
            todos = JSON.parse(storedTodos);
        }
        document.getElementById("input")?.addEventListener("keydown", (e) => { 
            if(e.key=="Enter") addTodo()
        });
    });

    // Save todos to localStorage whenever it changes
    $effect(()=>{
        localStorage.setItem("todos", JSON.stringify(todos))
    });

    const addTodo = () => {
        if (inputValue.trim().length === 0) return;
        todos = [...todos, inputValue];
        inputValue = "";
    };

    const removeTodo = (id: number) => {
        todos = todos.filter((_, index) => index !== id);
    };
</script>

<div class="px-4 min-h-screen">
    <Card class="max-w-md mx-auto shadow-lg border-0">
        <div class="p-4">
            <Heading tag="h1" class="text-center mb-6 text-gray-800 font-bold">My Todo List</Heading>
            
            <div class="flex gap-2 mb-6">
                <Input 
                    id="input" 
                    bind:value={inputValue} 
                    placeholder="What needs to be done?"
                    class="flex-1 dark:focus:ring-purple-600 focus:ring-purple-600 focus:border-purple-600 dark:focus:border-purple-600"
                    size="md"
                />
                <Button 
                    on:click={addTodo} 
                    size="md"
                    color='purple'
                >
                    <Plus class="mr-2 h-5 w-5" />
                    Add
                </Button>
            </div>
            
            {#if todos.length === 0}
                <div class="text-center py-6 text-gray-500">
                    No todos yet. Add one above!
                </div>
            {:else}
                <ul transition:slide  class="space-y-3">
                    {#each todos as todo, index}
                        <li transition:slide class="flex items-center justify-between p-3 rounded-lg border shadow-sm hover:shadow-md transition-shadow">
                            <span class="text-gray-800 dark:text-white">{todo}</span>
                            <Button 
                                on:click={() => removeTodo(index)} 
                                size="xs" 
                                color="purple" 
                                class="min-w-8"
                            >
                                <Trash class="h-4 w-4" />
                            </Button>
                        </li>
                    {/each}
                </ul>
                
                <div class="mt-4 text-center">
                    <Badge color="purple" class="px-3 py-1.5">
                        {todos.length} {todos.length === 1 ? 'item' : 'items'}
                    </Badge>
                </div>
            {/if}
        </div>
    </Card>
</div>

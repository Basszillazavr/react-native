# Мини-задачи по React Native для практики

## Задача 1: Простой счетчик

**Описание:** Создайте компонент, который отображает число и имеет две кнопки: "Увеличить" и "Уменьшить". При нажатии на кнопки число должно соответственно увеличиваться или уменьшаться.

**Пример:**

+-----------------+
|      10         |
+-------+---------+
| Увеличить | Уменьшить |
+-------+---------+


**Решение:**

```javascript
import React, { useState } from 'react';
import { StyleSheet, View, Text, Button } from 'react-native';

const CounterApp = () => {
  const [count, setCount] = useState(0);

  const incrementCount = () => {
    setCount(count + 1);
  };

  const decrementCount = () => {
    setCount(count - 1);
  };

  return (
    <View style={styles.container}>
      <Text style={styles.counterText}>{count}</Text>
      <View style={styles.buttonContainer}>
        <Button title="Увеличить" onPress={incrementCount} />
        <Button title="Уменьшить" onPress={decrementCount} />
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  counterText: {
    fontSize: 48,
    marginBottom: 20,
  },
  buttonContainer: {
    flexDirection: 'row',
    gap: 20,
  },
});

export default CounterApp;
Задача 2: Список задач (ToDo List)
Описание: Создайте простой компонент списка задач. Он должен иметь поле ввода для добавления новой задачи и кнопку "Добавить". Задачи должны отображаться в виде списка, и рядом с каждой задачей должна быть кнопка "Удалить".

Пример:

+-----------------+
| Ввести задачу... | [Добавить] |
+-----------------+
| - Купить молоко   | [Удалить]  |
| - Позвонить другу | [Удалить]  |
+-----------------+
Решение:

JavaScript

import React, { useState } from 'react';
import { StyleSheet, View, Text, TextInput, Button, FlatList, TouchableOpacity } from 'react-native';

const TodoApp = () => {
  const [task, setTask] = useState('');
  const [tasks, setTasks] = useState([]);

  const handleAddTask = () => {
    if (task.trim()) {
      setTasks([...tasks, task]);
      setTask('');
    }
  };

  const handleDeleteTask = (index) => {
    const newTasks = tasks.filter((_, i) => i !== index);
    setTasks(newTasks);
  };

  const renderItem = ({ item, index }) => (
    <View style={styles.listItem}>
      <Text>{item}</Text>
      <TouchableOpacity style={styles.deleteButton} onPress={() => handleDeleteTask(index)}>
        <Text style={styles.deleteButtonText}>Удалить</Text>
      </TouchableOpacity>
    </View>
  );

  return (
    <View style={styles.container}>
      <View style={styles.inputContainer}>
        <TextInput
          style={styles.input}
          placeholder="Ввести задачу..."
          value={task}
          onChangeText={setTask}
        />
        <Button title="Добавить" onPress={handleAddTask} />
      </View>
      <FlatList
        data={tasks}
        renderItem={renderItem}
        keyExtractor={(item, index) => index.toString()}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
  },
  inputContainer: {
    flexDirection: 'row',
    marginBottom: 20,
  },
  input: {
    flex: 1,
    borderWidth: 1,
    borderColor: '#ccc',
    padding: 10,
    marginRight: 10,
  },
  listItem: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    paddingVertical: 10,
    borderBottomWidth: 1,
    borderBottomColor: '#eee',
  },
  deleteButton: {
    backgroundColor: 'red',
    padding: 8,
    borderRadius: 5,
  },
  deleteButtonText: {
    color: 'white',
  },
});

export default TodoApp;
Задача 3: Переключение темы (Light/Dark Mode)
Описание: Создайте компонент, который позволяет пользователю переключаться между светлой и темной темой. Изменение темы должно менять цвет фона экрана и цвет текста.

Пример:

+-----------------+
| Текущая тема: Светлая |
| [Переключить тему] |
+-----------------+
(После переключения)
+-----------------+
| Текущая тема: Темная  |
| [Переключить тему] |
+-----------------+
Решение:

JavaScript

import React, { useState } from 'react';
import { StyleSheet, View, Text, Button } from 'react-native';

const ThemeSwitcherApp = () => {
  const [isDarkMode, setIsDarkMode] = useState(false);

  const toggleTheme = () => {
    setIsDarkMode(!isDarkMode);
  };

  const theme = isDarkMode
    ? { backgroundColor: 'black', color: 'white' }
    : { backgroundColor: 'white', color: 'black' };

  return (
    <View style={[styles.container, { backgroundColor: theme.backgroundColor }]}>
      <Text style={{ color: theme.color, marginBottom: 20 }}>
        Текущая тема: {isDarkMode ? 'Темная' : 'Светлая'}
      </Text>
      <Button title="Переключить тему" onPress={toggleTheme} />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default ThemeSwitcherApp;
Задача 4: Простой HTTP запрос (получение данных)
Описание: Создайте компонент, который при монтировании выполняет GET-запрос к публичному API (например, https://jsonplaceholder.typicode.com/todos/1) и отображает полученный title. Отобразите сообщение "Загрузка..." во время выполнения запроса и сообщение об ошибке, если запрос не удался.

Решение:

JavaScript

import React, { useState, useEffect } from 'react';
import { StyleSheet, View, Text } from 'react-native';

const FetchDataApp = () => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch('[https://jsonplaceholder.typicode.com/todos/1](https://jsonplaceholder.typicode.com/todos/1)');
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const json = await response.json();
        setData(json);
        setLoading(false);
      } catch (e) {
        setError(e.message);
        setLoading(false);
      }
    };

    fetchData();
  }, []);

  if (loading) {
    return <View style={styles.container}><Text>Загрузка...</Text></View>;
  }

  if (error) {
    return <View style={styles.container}><Text>Ошибка: {error}</Text></View>;
  }

  return (
    <View style={styles.container}>
      <Text style={styles.dataText}>Заголовок: {data?.title}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  dataText: {
    fontSize: 20,
  },
});

export default FetchDataApp;
Задача 5: Отображение списка изображений (с использованием FlatList)
Описание: Создайте компонент, который отображает список изображений. Используйте FlatList для эффективной отрисовки. Предоставьте массив URL-адресов изображений.

Пример:

+-----------------+
| [Изображение 1] |
| [Изображение 2] |
| [Изображение 3] |
+-----------------+
Решение:

JavaScript

import React from 'react';
import { StyleSheet, View, Image, FlatList } from 'react-native';

const imageURLs = [
  '[https://via.placeholder.com/150/FF0000](https://via.placeholder.com/150/FF0000)',
  '[https://via.placeholder.com/150/00FF00](https://via.placeholder.com/150/00FF00)',
  '[https://via.placeholder.com/150/0000FF](https://via.placeholder.com/150/0000FF)',
  '[https://via.placeholder.com/150/FFFF00](https://via.placeholder.com/150/FFFF00)',
];

const ImageListApp = () => {
  const renderItem = ({ item }) => (
    <Image source={{ uri: item }} style={styles.image} />
  );

  return (
    <View style={styles.container}>
      <FlatList
        data={imageURLs}
        renderItem={renderItem}
        keyExtractor={(item, index) => index.toString()}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 10,
  },
  image: {
    width: '100%',
    height: 200,
    marginBottom: 10,
    resizeMode: 'cover',
  },
});

export default ImageListApp;

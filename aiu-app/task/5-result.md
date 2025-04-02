```markdown
## Pinterest-подобная галерея в React Native Expo с Expo Router

Этот пример демонстрирует создание Pinterest-подобной галереи в React Native с использованием Expo Router, Expo Image, и Expo AsyncStorage. Мы сосредоточимся на загрузке и отображении изображений, обработке ошибок и переходе на экран деталей.

**Технологии:**

*   **React Native:** Для интерфейса.
*   **Expo:** Упрощает разработку и предоставляет удобные API.
*   **Expo Router:**  Для навигации.
*   **Expo Image:**  Для быстрой загрузки и кэширования изображений.
*   **Expo AsyncStorage:** Для сохранения данных (например, лайки).

**Структура проекта:**

```
pinterest-gallery/
├── app/
│   ├── _layout.js       # Главная раскладка для навигации
│   ├── index.js         # Главный экран (список изображений)
│   └── details/
│       └── [id].js    # Экран деталей изображения (динамический роут)
└── components/
├── ImageItem.js    # Компонент отдельного изображения
└── ErrorImagePlaceholder.js # Компонент для отображения ошибки загрузки
```

**Файлы:**

**1. `app/_layout.js` (Главная раскладка):**

```javascript
import { Stack } from 'expo-router';
import { SafeAreaProvider } from 'react-native-safe-area-context';

export default function Layout() {
  return (
    <SafeAreaProvider>
      <Stack>
        <Stack.Screen name="index" options={{ title: 'Pinterest Gallery' }} />
        <Stack.Screen name="details/[id]" options={{ title: 'Image Details' }} />
      </Stack>
    </SafeAreaProvider>
  );
}
```

**Объяснение:**

*   Настраивает стек навигации с двумя экранами: `index` (главная страница) и `details/[id]` (экран деталей с динамическим роутом по `id`).
*   Использует `SafeAreaProvider` для правильного отображения на устройствах с вырезами и рамками.

**2. `app/index.js` (Главный экран - список изображений):**

```javascript
import React, { useState, useEffect, useCallback } from 'react';
import { StyleSheet, View, Dimensions, ActivityIndicator, FlatList } from 'react-native';
import { useNavigation } from 'expo-router';
import ImageItem from '../../components/ImageItem';

const numColumns = 2;
const gap = 5;

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
  },
  listContent: {
    paddingHorizontal: gap / 2,
  },
});

export default function Index() {
  const columnWidth = (Dimensions.get('window').width - gap * (numColumns + 1)) / numColumns;
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);
  const navigation = useNavigation(); // Используем useNavigation из expo-router

  useEffect(() => {
    const loadInitialData = async () => {
      setLoading(true);
      const initialImages = await fetchImages(1);
      setData(initialImages);
      setLoading(false);
    };
    loadInitialData();
  }, []);

  const fetchImages = async (page) => {
    // Simulate API delay
    await new Promise(resolve => setTimeout(resolve, 500));
    return Array.from({ length: 10 }, (_, i) => ({
      id: `img-${page}-${i}`,
      uri: `https://picsum.photos/300/300?random=${Math.random()}`,
    }));
  };

  const loadMoreData = async () => {
    const newImages = await fetchImages(page + 1);
    setData((prev) => [...prev, ...newImages]);
    setPage((prev) => prev + 1);
  };

  const handleImagePress = useCallback((item) => {
    navigation.navigate(`details/${item.id}`); // Навигация с использованием id
  }, [navigation]);

  const renderItem = ({ item }) => (
    <ImageItem item={item} columnWidth={columnWidth} onPress={handleImagePress} />
  );

  return (
    <View style={styles.container}>
      {loading ? (
        <ActivityIndicator size="large" color="#0000ff" />
      ) : (
        <FlatList
          data={data}
          renderItem={renderItem}
          keyExtractor={(item) => item.id}
          numColumns={numColumns}
          onEndReached={loadMoreData}
          onEndReachedThreshold={0.5}
          contentContainerStyle={styles.listContent}
        />
      )}
    </View>
  );
}
```

**Объяснение:**

*   `useNavigation` хук из `expo-router` для навигации.
*   `handleImagePress` функция для навигации к экрану деталей, используя динамический роут `details/${item.id}`.

**3. `app/details/[id].js` (Экран деталей изображения - динамический роут):**

```javascript
import React, { useState, useEffect } from 'react';
import { StyleSheet, View, Text } from 'react-native';
import { useLocalSearchParams } from 'expo-router';
import { Image } from 'expo-image'; // Используем expo-image

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
  image: {
    width: 300,
    height: 300,
    borderRadius: 10,
  },
  text: {
    marginTop: 20,
    fontSize: 16,
  },
});

export default function Details() {
  const { id } = useLocalSearchParams(); // Получаем id из параметров роута
  const [imageUrl, setImageUrl] = useState(null);

  useEffect(() => {
      // Здесь нужно подставить реальный URL картинки по id
      // В данном примере мы просто генерируем случайный URL
      const randomUrl = `https://picsum.photos/300/300?random=${id}`;
      setImageUrl(randomUrl);
  }, [id]);


  return (
    <View style={styles.container}>
      {imageUrl ? (
        <Image
          style={styles.image}
          source={imageUrl}
          placeholder={require('../../assets/placeholder.png')} // Пример placeholder
        />
      ) : (
        <Text>Loading...</Text>
      )}
      <Text style={styles.text}>Image ID: {id}</Text>
    </View>
  );
}
```

**Объяснение:**

*   `useLocalSearchParams` хук из `expo-router` для получения параметров роута (в данном случае `id`).
*   Динамически генерирует URL изображения на основе `id`.  В реальном приложении здесь нужно будет обращаться к API.
*   Использует `expo-image` для отображения изображения и placeholder.

**4. `components/ImageItem.js` (Компонент отдельного изображения):**

```javascript
import React, { useState, useCallback } from 'react';
import { StyleSheet, View, TouchableOpacity } from 'react-native';
import { Image } from 'expo-image';
import { ActivityIndicator } from 'react-native';
import ErrorImagePlaceholder from './ErrorImagePlaceholder';

const styles = StyleSheet.create({
  itemContainer: {
    flex: 1,
    margin: 2.5,
  },
  image: {
    borderRadius: 10,
    backgroundColor: '#eee',
  },
  loadingIndicator: {
    position: 'absolute',
    top: 0,
    left: 0,
    right: 0,
    bottom: 0,
    justifyContent: 'center',
    alignItems: 'center',
  },
  errorContainer: {
    position: 'absolute',
    top: 0,
    left: 0,
    right: 0,
    bottom: 0,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: 'rgba(0,0,0,0.5)',
  },
  errorText: {
    color: 'white',
  },
});

const ImageItem = React.memo(({ item, columnWidth, onPress }) => {
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(false);

  const handleLoadStart = useCallback(() => {
    setLoading(true);
    setError(false); // Reset error state on load start
  }, []);

  const handleLoad = useCallback(() => {
    setLoading(false);
  }, []);

  const handleError = useCallback(() => {
    setLoading(false);
    setError(true);
  }, []);

  return (
    <View style={styles.itemContainer}>
      <TouchableOpacity onPress={() => onPress(item)}>
        <Image
          style={[styles.image, { width: columnWidth, height: columnWidth }]}
          source={item.uri}
          resizeMode="cover"
          onLoadStart={handleLoadStart}
          onLoad={handleLoad}
          onError={handleError}
        />
        {loading && (
          <View style={styles.loadingIndicator}>
            <ActivityIndicator size="small" color="#0000ff" />
          </View>
        )}
        {error && (
          <View style={styles.errorContainer}>
            <ErrorImagePlaceholder />
          </View>
        )}
      </TouchableOpacity>
    </View>
  );
});

export default ImageItem;
```

**Объяснение:**

*   Использует `expo-image` для отображения изображений.
*   Обрабатывает ошибки загрузки и отображает `ErrorImagePlaceholder`.
*   Отображает индикатор загрузки.
*   `React.memo` для оптимизации перерисовок.

**5. `components/ErrorImagePlaceholder.js` (Компонент для отображения ошибки загрузки):**

```javascript
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  container: {
    backgroundColor: 'rgba(0,0,0,0.7)',
    borderRadius: 10,
    padding: 10,
    alignItems: 'center',
    justifyContent: 'center',
  },
  text: {
    color: 'white',
    fontSize: 12,
  },
});

const ErrorImagePlaceholder = () => {
  return (
    <View style={styles.container}>
      <Text style={styles.text}>Error loading image</Text>
    </View>
  );
};

export default ErrorImagePlaceholder;
```

**Установка:**

1.  Создайте проект Expo: `npx create-expo-app -t tabs pinterest-gallery` (выберите `blank` или `tabs` template).
2.  Установите необходимые библиотеки:

    ```bash
    npx expo install expo-router expo-image react-native-safe-area-context expo-status-bar
    ```
3. Создайте структуру папок и файлы, как описано выше.
4.  Вставьте код в соответствующие файлы.
5.  Запустите приложение: `npx expo start`.

**Основные моменты и улучшения:**

*   **Expo Router:** Использование `expo-router` для навигации, особенно для динамических роутов, упрощает структуру проекта и обеспечивает декларативный подход к навигации.
*   **Expo Image:**  `expo-image` предоставляет быструю загрузку и кэширование изображений, улучшая производительность и пользовательский опыт.  Обязательно используйте placeholder для лучшей визуальной обратной связи.
*   **Динамические роуты:**  `details/[id].js` демонстрирует использование динамических роутов для перехода на экран деталей изображения на основе его `id`.
*   **Обработка ошибок и индикаторы загрузки:**  Важно обрабатывать ошибки загрузки изображений и предоставлять индикаторы загрузки для улучшения пользовательского опыта.

**Дальнейшее развитие:**

*   **Реальный API:** Подключитесь к реальному API для получения данных об изображениях.
*   **Лайки и сохранения:** Используйте `Expo AsyncStorage` для хранения информации о лайках и сохранениях.
*   **Улучшенная обработка ошибок:** Добавьте кнопку "Retry" для повторной загрузки изображения.
*   **Анимации:**  Используйте `react-native-reanimated` для добавления плавных анимаций.
*   **Пагинация:** Реализуйте пагинацию на сервере и клиенте для эффективной загрузки большого количества изображений.
*   **Masonry Layout:** Используйте библиотеку, такую как `react-native-masonry-layout`, для создания настоящего Pinterest-подобного макета.

Этот пример предоставляет прочную основу для создания Pinterest-подобной галереи в React Native Expo с использованием современных инструментов и лучших практик.
```

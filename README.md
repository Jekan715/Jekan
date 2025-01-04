<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Автоматический сумматор</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      margin-top: 50px;
    }
    textarea {
      padding: 10px;
      font-size: 16px;
      width: 90%;
      height: 300px;
    }
    .result, .history {
      margin-top: 20px;
      font-size: 20px;
    }
    .result {
      color: green;
    }
    .history {
      color: blue;
    }
    button {
      padding: 5px 10px;
      font-size: 14px;
      margin-left: 5px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <h1>Автоматический сумматор</h1>
  <button onclick="pasteText()">Вставить</button>
  <textarea 
    id="numberInput" 
    placeholder="Вставьте ваш текст с числами"
    oninput="updateSum()"></textarea>
  <div class="result">
    <p>Итоговая сумма: <span id="totalSum">0</span></p>
  </div>
  <div class="history">
    <h2>История итогов:</h2>
    <ul id="historyList"></ul>
  </div>

  <script>
    async function pasteText() {
      try {
        // Получаем текст из буфера обмена
        const text = await navigator.clipboard.readText();
        // Вставляем текст в текстовое поле
        document.getElementById("numberInput").value = text;
        // Обновляем сумму после вставки текста
        updateSum();
      } catch (err) {
        alert("Не удалось вставить текст. Проверьте разрешения на доступ к буферу обмена.");
      }
    }

    function updateSum() {
      const input = document.getElementById("numberInput").value;

      // Убираем строки с именами и датами, оставляем только числа
      const cleanedInput = input
        .replace(/^.*\[.*\].*$/gm, "") // Удаляем строки с именами и датами
        .replace(/[^\d\n]/g, "")      // Убираем символы, оставляем только цифры и переносы строк
        .replace(/\n{2,}/g, "\n\n")   // Заменяем несколько переносов строки на двойной перенос
        .trim();                      // Убираем лишние пробелы и переносы в начале и конце

      // Разделяем строки на числа
      const numbers = cleanedInput
        .split(/\n+/)                // Разделяем по переносам строк
        .map(num => parseFloat(num)) // Преобразуем в числа
        .filter(num => !isNaN(num)); // Оставляем только числа

      // Считаем сумму чисел
      const sum = numbers.reduce((acc, num) => acc + num, 0);

      // Обновляем текстовое поле и сумму
      document.getElementById("numberInput").value = cleanedInput; // Показываем очищенный текст
      document.getElementById("totalSum").innerText = sum;         // Обновляем итоговую сумму

      // Автоматически сохраняем итог в истории
      saveToHistory(sum);
    }

    function saveToHistory(sum) {
      if (sum === 0) return; // Пропускаем пустую сумму

      const historyList = document.getElementById("historyList");
      const newHistoryItem = document.createElement("li");

      // Создаем текст с кнопкой удаления
      newHistoryItem.innerHTML = `Итог: ${sum} <button onclick="removeHistoryItem(this)">Удалить</button>`;
      
      // Добавляем новый элемент в список
      historyList.appendChild(newHistoryItem);
    }

    function removeHistoryItem(button) {
      // Удаляем родительский элемент кнопки (li)
      const item = button.parentElement;
      item.remove();
    }
  </script>
</body>
</html>
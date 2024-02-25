Демо выполненного задания 
http://polakiv.theweb.place/index.html


API v1.02

Аутентификация запросов.

Аутентификация запросов происходит за счет авторизационной строки, которая передается в параметре заголовка X-Auth.

Значение X-Auth формируется по следующему шаблону:
md5(пароль_таймштамп)
Таймштамп представляет собой текущую дату UTC с точностью до дня в формате год, месяц, день без разделителей.
Пароль и таймштамп разделяются символом подчеркивания.
Пример:
md5("password_20230821")

В случае, если авторизационная строка сформирована некорректно, будет возвращен HTTP код 401

Методы.

Работа с api осуществляется через POST на указанный адрес и порт. Тело запроса представляет собой json содержащий имя метода action и опциональный набор параметров params.
Пример:
{
	"action": "filter",
	"params": {"price": 17500.0}
}

Если запрос не содержит ошибок, на него приходит синхронный ответ в формате json. Результат работы метода возвращается по ключу result.
Пример:
{
	"result": [
	"59a9b5b4-6546-417f-9c0a-7ec1b9385af1",
	"271aa4c2-70be-4a03-9e20-1fbebb2aa79f",
	"12d19cdd-a58a-41bf-b387-3c8c3d08a40a",
	]
}

Если в запросе содержаться ошибки, то будет возвращен HTTP код 400

get_ids - метод возвращает упорядоченный список идентификаторов товаров. Далее по выбранным идентификаторам можно запросить подробную информацию о товаре. По умолчанию возвращает идентификаторы всех имеющиеся товаров.
Параметры:
offset - положительное целое число. Определяет смещение относительно начала списка.
limit - положительное целое число. Определяет желаемое число возвращаемых записей.

Пример запроса:
{
	"action": "get_ids",
	"params": {"offset": 10, "limit": 3}
}

Пример ответа:
{
	"result": [
	"18e4e3bd-5e60-4348-8c92-4f61c676be1f",
	"711837ec-57f6-4145-b17f-c74c2896bafe",
	"6c972a4a-5b91-4a98-9780-3a19a7f41560"
	]
}


get_items - возвращает упорядоченный список товаров со всеми характеристиками, если переданы идентификаторы товаров. Максимум 100 записей. Если нужно получить более 100, необходимо выполнить запрос несколько раз. Без передачи идентификаторов возвращает null
Параметры:
ids - упорядоченный список строк. Определяет идентификаторы товаров, которые будут возвращены.
Пример запроса:
{
	"action": "get_items",
	"params": {"ids": ["1789ecf3-f81c-4f49-ada2-83804dcc74b0"]}
}

Пример ответа:
{
	"result": [
		{
		"brand": null,
		"id": "1789ecf3-f81c-4f49-ada2-83804dcc74b0",
		"price": 16700.0,
		"product": "Золотое кольцо с бриллиантами"
		}
	]
}



get_fields - без параметров возвращает упорядоченный список имеющихся полей товаров. При передаче параметра field возвращает упорядоченный список значений данного поля товаров.
Параметры:
field - строка. Должна содержать действительное название поля товара.
offset - положительное целое число. Определяет смещение относительно начала списка.
limit - положительное целое число. Определяет желаемое число возвращаемых записей.
Пример запроса:
{
	"action": "get_fields",
	"params": {"field": "brand", "offset": 3, "limit": 5}
}

Пример ответа:
{
	"result": [
	null,
	null,
	"Piaget",
	null,
	null
	]
}

filter - используется для фильтрации. Возвращает упорядоченный список идентификаторов товаров, соответствующих заданному значению.
Параметры:
В качестве параметра может использоваться любое поле возвращаемое методом get_fields без параметров. В качестве значения должен использоваться тип данных соответствующий полю. Для поля product будет проверяться вхождение параметра в строку. Для остальных полей проверяется строгое соответствие.
Пример запроса:
{
	"action": "filter",
	"params": {"price": 17500.0}
}

Пример ответа:
{
	"result": [
	"59a9b5b4-6546-417f-9c0a-7ec1b9385af1",
	"271aa4c2-70be-4a03-9e20-1fbebb2aa79f",
	]
}


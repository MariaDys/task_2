# task_2

Посчитаю MD5 для всех файлов в папке static через команду `md5 *`. Номер по списку 5 => для анализа беру файлы 5 и 11.

MD5 (05) = 1bb574143344faa4cadce4c5563**26d21**
\
MD5 (11) = 36d424de2e9edbeb03d8081147b**dedf6**

Проводить анализ буду в Cutter (аналог Hiew, подходит для macos). Для этого Cutter -> Open file -> static/05 (аналогично для static/11)

<img width="500" height="500" alt="Снимок экрана 2025-12-25 в 01 52 11" src="https://github.com/user-attachments/assets/1d56dc40-5963-4bc9-8351-28bb0ae34f7e" />
<img width="500" height="500" alt="Снимок экрана 2025-12-25 в 01 52 28" src="https://github.com/user-attachments/assets/ccbb3041-95c8-4d02-a507-812a071af52f" />

## 1. Контрольные суммы

В Dashboard в разделе Хэши видим контрольные суммы MD5/SHA1/SHA256.

| Файл | MD5  | SHA1 | SHA256
|------------|------------|------------|------------|
| 05 | 1bb574143344faa4cadce4c556326d21 | f8e8756932c034517f53aac460b1a2176d2d2f2b  |1a56c95647f48fc36e0a40c8d153667e21399323534bc5e079c2fe9df87ef19b |
| 11 | 36d424de2e9edbeb03d8081147bdedf6 | 4212c9e359634e1dc58c18297c4a75c59b7734c4  | 770bb2547fdd78b0a701ffc7f0bae3c31d17ffcf423fed1832040ed14956513b |


## 2. Как файл детектируется
## 3. Формат файла

В Dashboard в разделе Информация смотрим на поля Формат, Биты, Тип, Архитектура, ОС, Подсистема, Порядок байт. Выводы в таблице:
| Файл | Формат
|------------|------------|
| 05 | PE32-исполняемый файл (Portable Executable), предназначен для ОС Windows. Имеет 32-битную разрядность, для архитектуры Intel x86 (i386). Тип файла XEC (Executable), графическое приложение, так как Windows GUI, порядок байт Little Endian. |
| 11 | PE32-исполняемый файл (Portable Executable), предназначен для ОС Windows. Имеет 32-битную разрядность, для архитектуры Intel x86 (i386). Тип файла XEC (Executable), графическое приложение, так как Windows GUI, порядок байт Little Endian. | 


## 4. Дата компиляции файла

Можно посмотреть в Dashboard в поле Скомпилирован. Второй вариант: добавим вид Заголовки: Окна -> Информация -> Заголовки. Ориентируемся на заголовок TimeDataStamp:

<img width="500" height="500" alt="Снимок экрана 2025-12-25 в 02 00 37" src="https://github.com/user-attachments/assets/572c4bb0-348b-4c12-8593-39a266e142a4" />
<img width="500" height="500" alt="Снимок экрана 2025-12-25 в 02 01 04" src="https://github.com/user-attachments/assets/9ddbb753-fbd9-45c8-8b63-de18e7147a4e" />

| Файл | Dashboard: Скомпилирован | Заголовок TimeDataStamp |
|------------|------------|------------|
| 05 | Fri Apr 21 10:13:08 2023 UTC+3|0x64423784 (dec: 1682061188)|
| 11 | Thu Oct 30 20:40:24 2014 UTC+3|0x54527808 (dec: 1414690824)|

TimeDataStamp - это число секунд, прошедших с 01.01.1970 00:00:00 UTC. Переведем hex в dec, зайдем на https://www.unixtimestamp.com и конвертируем число секунд в конкретные даты:
<img width="500" height="500" alt="Снимок экрана 2025-12-25 в 02 09 00" src="https://github.com/user-attachments/assets/077d959e-4ae6-4b1f-a4c0-0eb2bac19cf3" />
<img width="500" height="500" alt="Снимок экрана 2025-12-25 в 02 09 22" src="https://github.com/user-attachments/assets/75bb506a-f0a8-490a-8133-ae240cfaea9c" />


### 5. Импортируемые библиотеки и функции

Импортируемые библиотеки смотрю в Dashboard в разделе Библиотеки. Функции смотрю в окне Импорт:
<img width="500" height="500" alt="Снимок экрана 2025-12-25 в 02 28 05" src="https://github.com/user-attachments/assets/e0c8c54f-addc-45fe-ab9d-92f8da678035" />
<img width="500" height="500" alt="Снимок экрана 2025-12-25 в 02 28 14" src="https://github.com/user-attachments/assets/59719f26-667b-45e9-9c73-201ecab27538" />


Для файла 05:
| Библиотека | Описание библиотеки | Windows API функции |
|------------|------------|------------|
| kernel32.dll | Управление памятью| StrToIntExA (преобразование строк в числовые значения)|
| shlwapi.dll | Работа с элементами пользовательского интерфейса| DialogBoxParamA, EndDialog, GetDIgltem, GetWindowTextA, GetWindowTextLengthA, MessageBoxA (функции в целом: диалоговые окна и вывод сообщений пользователю) |
| user32.dll | Работа с графическим интерфейсом|ExitProcess (завершение выполнения программы), GetModuleHandleA (получение информации о модуле), VirtualAlloc (динам выделение памяти), VirtualFree (динам освобождение памяти)|

Для файла 11:

| Библиотека | Описание библиотеки | Windows API функции |
|------------|--------------------|---------------------|
| kernel32.dll | Управление памятью | CreateFileW, WriteFile, CloseHandle, SetFilePointerEx, FlushFileBuffers, ExitProcess, TerminateProcess, Sleep, GetCurrentProcess, GetCurrentProcessId, GetCommandLineA, GetStartupInfoW, GetEnvironmentStringsW, FreeEnvironmentStringsW, VirtualAlloc, VirtualFree, HeapAlloc, HeapFree, HeapReAlloc, HeapSize, GetProcessHeap, LoadLibraryExW, GetProcAddress, GetModuleHandleW, GetModuleHandleExW, GetModuleFileNameW, GetModuleFileNameA, IsDebuggerPresent, IsProcessorFeaturePresent, OutputDebugStringW, QueryPerformanceCounter, GetSystemTimeAsFileTime, InitializeCriticalSectionEx, InitializeCriticalSectionAndSpinCount, EnterCriticalSection, LeaveCriticalSection, DeleteCriticalSection, RaiseException, UnhandledExceptionFilter, SetUnhandledExceptionFilter, RtlUnwind, EncodePointer, DecodePointer |
| user32.dll | Работа с графическим интерфейсом | ShowWindow, SetWindowTextW, SetWindowPos, SetWindowLongW, SendMessageW, MessageBoxW, DialogBoxParamW, EndDialog, PostQuitMessage, IsWindow, GetWindowTextW, GetWindowTextLengthW, GetWindowRect, GetClientRect, ClientToScreen, ScreenToClient, AdjustWindowRectEx, GetSystemMetrics, GetMenu, GetKeyState, GetDlgItem, LoadImageW, LoadIconW, LoadBitmapW, GetDC |
| gdi32.dll | Работа с устройствами вывода (GDI) | CreateFontW, CreateCompatibleDC, BitBlt, SelectObject, GetObjectW, DeleteDC |
| comctl32.dll | Инициализация стандартных элементов управления Windows | InitCommonControls |


### 6. Строки, указывающие на функциональность файла

Смотрим на строки в окне Strings:

<img width="500" height="500" alt="Снимок экрана 2025-12-25 в 02 44 15" src="https://github.com/user-attachments/assets/2412aaeb-7dcb-4cf4-a741-23ee6471442d" />
<img width="500" height="500" alt="Снимок экрана 2025-12-25 в 02 44 21" src="https://github.com/user-attachments/assets/5c0f5d46-0e3f-464d-ad56-9c89a8bde44f" />

Можно фильтровать строки по разделу
.text: основные функции и методы программы
.data: инициализированные глобальные, статические переменные, сообщения пользователю, ошибки
.rdata: read-only data
.rsrc: ресурсы приложения (GUI)
.idata: таблица импорта

**Файл 05:**

<img width="500" height="300" alt="Снимок экрана 2025-12-25 в 02 54 19" src="https://github.com/user-attachments/assets/f57118b3-b0de-4687-8b94-7e520201dec9" />
<img width="500" height="300" alt="Снимок экрана 2025-12-25 в 02 54 27" src="https://github.com/user-attachments/assets/2b2524fd-35fb-4e05-9976-75a9c9cafb0a" />

Пока можем сделать вывод, что файл -- это олимпиадное задание CTF (.text: "All-Russian Student Olympiad on Information Security 2023", .rsrc: "Olympiad Task #1"). Кроме того, по строкам секции .data видим, что при взаимодействии программы с пользователем она проверяет его ввод (Input error, The line must not be empty!...), есть валидация ввода на длину, формат, проверяется вводимый ключ.

**Файл 11:**

Проанализируем разделы .data, .rdata, .rsrc.
Для анализа взаимодействия программы и пользователя можно посмотреть сообщения об ошибках которые программа может выдавать, реакции программы на ввод пользователя, валидацию.
<img width="300" height="300" alt="Снимок экрана 2025-12-25 в 03 11 08" src="https://github.com/user-attachments/assets/eb7044b4-3e42-48b1-8fe9-c6d95af32906" />

Я открыла файл в IDA, перешла к основной функции WinMain



### 7. Выводы о функциональности файла
Файл 05: Олимпиадное задание с проверкой ключа.
Файл 11:

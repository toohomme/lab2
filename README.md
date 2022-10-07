## АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #1 выполнил(а):
- Еремин Егор Константинович
- РИ210940
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | # | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)


## Цель работы
познакомиться с программными средствами для организции передачи данных между инструментами google, Python и Unity

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity.

Ход работы: 

1. В облачном сервисе Google Console подключаем API для работы с google sheets и google drive.

2. Реализовать запись данных из скрипта на python в google-таблицу.

Код на Python:

```py
import gspread
import numpy as np

gc = gspread.service_account(filename='unitydatascience-364121-126f92e27732.json')
sh = gc.open("UnitySheets")
price = np.random.randint(2000, 10000, 11)
mon = list(range(1, 11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        tempInf = ((price[i - 1] - price[i - 2]) / price[i - 2]) * 100
        tempInf = str(tempInf)
        tempInf = tempInf.replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i - 1]))
        sh.sheet1.update(('C' + str(i)), str(tempInf))
        print(tempInf)
```

Скриншот данных из консоли:

![image](https://user-images.githubusercontent.com/102966721/194571588-dd9dc5a4-be55-49ee-b84e-e2b4f5bb6c58.png)

Скриншот данных из Google sheets:

![image](https://user-images.githubusercontent.com/102966721/194571680-850a4a40-7d87-4101-905d-78ecac977f96.png)


3. Создать новый проект на Unity, который будет получать данные из google- таблицы, в которую были записаны данные в предыдущем пункте.
Написать функционал на Unity, в котором будет воспризводиться аудио- файл в зависимости от значения данных из таблицы.
Все файлы будут приложены в архиве.

Код на C#:

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;


public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i !=dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i !=dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i !=dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1KQ4ToD6hnqkOxdmesbtnx9rlEA2Ww6SHEcBjWIRRinA/values/Лист1?key=AIzaSyA7YcZ7w3CIKPBNcXhVzYhRCKbZk8zCbyI");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }

    }
    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```

Скриншот из проекта Unity:

![image](https://user-images.githubusercontent.com/102966721/194576775-e352d8d4-ddf4-4cf5-b7a7-b2f7fcf3598e.png)


## Задание 2
1. Как и предыдущем задании подключаем API для работы с Google sheets и Google drive

2. Код на Python:

```py
import gspread
import numpy as np
import matplotlib.pyplot as plt

gc = gspread.service_account(filename='task2-364812-168363f2c3a2.json')
sh = gc.open("Task2Sheets")


def model(a, b, x):
    return a * x + b


def loss_function(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    return (0.5 / num) * (np.square(prediction - y)).sum()


def optimize(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    da = (1.0 / num) * ((prediction - y) * x).sum()
    db = (1.0 / num) * ((prediction - y).sum())
    a = a - Lr * da
    b = b - Lr * db
    return a, b


def iterate(a, b, x, y, times):
    for i in range(times):
        a, b = optimize(a, b, x, y)
    return a, b


mon = list(range(1, 11))
i=0
while i <=len(mon):
    i +=1
    if i ==0:
        continue
    else:
        Lr = 0.000001
        x = [3, 21, 22, 34, 54, 34, 55, 67, 89, 99]
        x = np.array(x)
        y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
        y = np.array(y)
        a = np.random.rand(1)
        sh.sheet1.update(('A' + str(i)), str(a))
        
        b = np.random.rand(1)
        sh.sheet1.update(('B' + str(i)), str(b))
        
        a, b = iterate(a, b, x, y, 1)
        prediction = model(a, b, x)
        
        loss = loss_function(a, b, x, y)
        sh.sheet1.update(('C' + str(i)), str(loss))
        
        print(a, b, loss)

```

Данные из консоли:

![image](https://user-images.githubusercontent.com/102966721/194575489-2fa59333-32ad-472c-a911-6e51a0ec94f9.png)


Данные в Google sheets:

![image](https://user-images.githubusercontent.com/102966721/194575545-d4e8b1eb-9678-493f-8c85-78dd95aa6cf7.png)






## Задание 3
### Какова роль параметра Lr? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ. В качестве эксперимента можете изменить значение параметра.

- Перечисленные в этом туториале действия могут быть выполнены запуском на исполнение скрипт-файла, доступного [в репозитории](https://github.com/Den1sovDm1triy/hfss-scripting/blob/main/ScreatingSphereInAEDT.py).
- Для запуска скрипт-файла откройте Ansys Electronics Desktop. Перейдите во вкладку [Automation] - [Run Script] - [Выберите файл с именем ScreatingSphereInAEDT.py из репозитория].

```py

import ScriptEnv
ScriptEnv.Initialize("Ansoft.ElectronicsDesktop")
oDesktop.RestoreWindow()
oProject = oDesktop.NewProject()
oProject.Rename("C:/Users/denisov.dv/Documents/Ansoft/SphereDIffraction.aedt", True)
oProject.InsertDesign("HFSS", "HFSSDesign1", "HFSS Terminal Network", "")
oDesign = oProject.SetActiveDesign("HFSSDesign1")
oEditor = oDesign.SetActiveEditor("3D Modeler")
oEditor.CreateSphere(
	[
		"NAME:SphereParameters",
		"XCenter:="		, "0mm",
		"YCenter:="		, "0mm",
		"ZCenter:="		, "0mm",
		"Radius:="		, "1.0770329614269mm"
	], 
)

```

## Выводы

Абзац умных слов о том, что было сделано и что было узнано.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**

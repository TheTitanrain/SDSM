# 7. Управление перегрузками \(Congestion Management\)

Когда всё плохо, приоритет обработки следует отдать более важному трафику. Важность каждого пакета определяется на этапе классификации.   
Но что такое плохо?  
Необязательно все буферы должны быть забиты, чтобы приложения начали испытывать проблемы.  
Самый простой пример — голосовые пакетики, которые толпятся за большими пачками крупных пакетов приложения, скачивающего файл.  
Это увеличит задержку, испортит джиттер и, возможно, вызовет отбрасывания.  
То есть мы имеем проблемы с обеспечением качественных услуг при фактическом отсутствии перегрузок.  
Эту проблему призван решить механизм управления перегрузками \(Congestion Management\).  
Трафик разных приложений разделяется по очередям, как мы уже видели выше.  
Вот только в результате всё снова должно слиться в один интерфейс. Сериализация всё равно происходит последовательно.  
Каким же образом разным очередям удаётся предоставлять различный уровень сервисов?  
По-разному изымать пакеты из разных очередей.  
Занимается этим диспетчер.  
  
Мы рассмотрим большинство существующих сегодня диспетчеров, начиная с самого простого:

* **FIFO** — только одна очередь, все в BE, С — несправедливость.

  {% page-ref page="fifo-first-in-first-out.md" %}

* **PQ** — дорогу олигархам, холопы уступают.

  {% page-ref page="pq-priority-queuing.md" %}

* **FQ** — все равны.

  {% page-ref page="fq-fair-queuing.md" %}

* **RR -** все равны только на бумаге

  {% page-ref page="rr-round-robin.md" %}

* **WFQ, DWRR** — все равны, но некоторые ровнее.

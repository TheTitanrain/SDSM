# Что с тобой не так, IP?!

Я не зря взял в пример Ethernet-кадр. С IP совсем другая история.  
MAC-коммутация — это просто: ни тебе агрегации маршрутов, ни тебе Longest Prefix Match — только 48 уникальных бит.  
А вот в IP это всё есть. У нас может быть несколько маршрутов в Таблице Маршрутизации с разными длинами масок и выбрать нужно наидлиннейшую. Это базовый принцип IP-маршрутизации, с которым не поспоришь и не обойдёшь.  
Кроме того есть сложные ACL с их Wildcard-масками.

Долгое время решения этой проблемы не существовало. На заре сетей с пакетной коммутацией IP-пакеты обрабатывались на CPU. И главная проблема этого — даже не коммутация на скорости линии \(хотя и она тоже\), а влияние дополнительных настроек на производительность. Вы и сейчас можете это увидеть на каком-нибудь домашнем микротике, если настроить на нём с десяток ACL — сразу заметите, как просядет пропускная способность.  
Интернет разрастался, политик становилось всё больше, а требования к пропускной способности подпрыгивали скачкообразно, и CPU становился камнем преткновения. Тем более учитывая, что поиск маршрута подчас приходилось делать не один раз, а рекурсивно погружаться всё глубже.

Так в лихие 90-е зародился MPLS. Какая блестящая идея — построить заранее путь на Control Plane. Адресацией в MPLS будет метка фиксированной длины, и соответственно нужна единственная запись в таблице меток, что с пакетом дальше делать. При этом мы не теряем гибкости IP, поскольку он лежит в основе, и можем использовать CAM. Плюс заголовок MPLS — короток \(4 байта против 20 в IP\) и предельно прост.

Однако по иронии судьбы в то же время инженеры совершили прорыв, разработав TCAM — Ternary CAM. И с тех пор ограничений уже почти не было \(хотя не без оговорок\).

Подробнее от TCAM [дальше](../4.-tipov-chipov/tcam-ternary-content-addressable-memory.md).

Что же до MPLS, который ввиду данного события должен был скоропостижно скончаться, едва родившись, то он прорубил себе дверь в другой дом. Но об этом мы уже [наговорились](http://linkmeup.ru/blog/154.html).


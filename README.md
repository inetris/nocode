# Nocode
Модуль для 1С-Битрикс, позволяющий редакторам, контент-менеджерам описывать многоступенчатые условия выдачи контента для рекомендательных виджетов, подборок, рассылок, rss-лент без привлечения технических специалистов


### Принцип Работы
 
Этап 1. В начале определяются все значимые исходные данные - критерии - для того, чтобы точнее определить возможные интересы посетителя.  
Этап 2. Затем ищутся правила, в которых упоминаются эти критерии как входные и те значения, которые им присвоены  
Этап 3. После ищем уже товары (новости, рекламные материалы) по выходным условиям этих правил  
Этап 4. Подходящих товаров можно найти очень много, поэтому здесь нужны сортировка и ограничение по количеству  
Например, в правиле можно указать, что показать нужно 2 самых просматриваемых товара - сначала сортируем по количеству просмотров, затем берём первые два

### Тезаурус

Критерий - признак (посетителя, его местоположения, истории просмотров, конкретной новости, времени суток, сезона)
1) значимые - указаны хотя бы в одном правиле
2) незначимые - не учитываются

Условие - формально определённое требование.
1) Входные -  необходимые для срабатывания правила условия в виде связок "критерий-значение"
2) Выходные - условия для отбора новостей
3) Сортировка - критерий и направление сортировки по нему.
4) Предельное количество - лимит на количество материалов выбираемых по выходным условиям

Правило - совокупность условий, которые должны сработать все вместе. Результатом срабатывания правила является набор элементов (новостей или товаров, например).

### Допущения

1) Правил с одинаковыми входными параметрами может быть несколько.
2) Правила срабатывают в порядке убывания приоритета. В самом конце можно ставить заглушку - правило, которое в любом случае доберёт количество новостей до нужного, если не набралось по правилам до него.  
3) Если Значение не заполнено, будут выбраны элементы с таким же значением параметра, что у исходного
4) Сначала идёт сортировка всегда, потом - ограничение по количеству
5) На входе должен быть полный и достаточный набор критериев
Если критерий признан значимым, но в правиле его нет - оно сработает.

### Использование
_(array)_ $context - значения контекста, учитывающиеся при выборе правила. Ключи отдельных суперглобальных массивов берутся напрямую
_(int)_ $targetIblockId - идентификатор инфоблока, из которого будут выбраны элементы
_(string)_ $rulesCategory - секция с правилами. В идеале должно быть охвачено всё множество вариантов, чтобы сработало хотя бы одно правило
_(int)_ $cacheTime - время кеширования. Относится только к элементам целевого инфоблока
_(int)_ $maxCount - максимальное количество элементов для всех сработавших правил
_(bool)_ $forMassMode - режим 


```php
use Inetris\Nocode\DTO\Settings;
use Bitrix\Main\DI\ServiceLocator;

if (Loader::includeModule("inetris.nocode")) {
    $dto = new Settings($context, $targetIblockId, $rulesCategory, $cacheTime, $maxCount, $forMassMode);
    ServiceLocator::getInstance()->addInstance("inetris.nocode.settings", $dto);

    $related_news = ServiceLocator::getInstance()->get('inetris.nocode.nocode');
    $arrFilter['ID'] = $related_news->get();
}
```



### <a name="cacheskuname"></a>cacheSKUName
Cenová úroveň nové Azure Redis Cache.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Redis Cache."
      }
    },

Šablona definuje hodnoty, které jsou povoleny pro tento parametr (Basic nebo Standard) a přiřadí výchozí hodnotu (Basic), pokud není zadaná žádná hodnota. Basic poskytuje jeden uzel s více velikostí, které jsou k dispozici až do 53 GB.
Standard poskytuje dva uzly primární/replika s více velikostí, které jsou k dispozici až do 53 GB a 99,9 % SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily
Dané řadě pro verze sku.

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
Velikost novou instanci služby Azure Redis Cache. 

    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Redis Cache instance. "
      }
    }


Šablona definuje hodnoty, které jsou povoleny pro tento parametr (0, 1, 2, 3, 4, 5 nebo 6) a přiřadí výchozí hodnota (0), pokud není zadaná žádná hodnota. Tyto čísla odpovídají následující velikosti mezipaměti: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB


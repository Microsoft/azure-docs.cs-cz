---
title: Spuštění exportu vyvoláním příkazu $export v rozhraní Azure API pro FHIR
description: Tento článek popisuje, jak exportovat FHIR data pomocí $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: cavoeg
ms.openlocfilehash: a5b3daa499546f3a30b5a4d133d77786a1916b6a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559192"
---
# <a name="how-to-export-fhir-data"></a>Jak exportovat FHIR data


Funkce hromadného exportu umožňuje exportovat data ze serveru FHIR podle [specifikace FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Před použitím $export se ujistěte, že rozhraní Azure API pro FHIR je nakonfigurované tak, aby ho bylo možné používat. Pokud chcete konfigurovat nastavení exportu a vytvořit účet úložiště Azure, přečtěte si [stránku konfigurace dat pro export](configure-export-data.md).

## <a name="using-export-command"></a>Použití příkazu $export

Po nakonfigurování rozhraní API Azure pro FHIR pro export můžete pomocí příkazu $export exportovat data mimo službu. Data budou uložena do účtu úložiště, který jste zadali při konfiguraci exportu. Pokud se chcete dozvědět, jak vyvolat $export příkaz na serveru FHIR, přečtěte si dokumentaci ke [specifikaci změněného HL7 FHIR $export](https://hl7.org/Fhir/uv/bulkdata/export/index.html).


**Úlohy jsou zablokované ve špatném stavu.**

V některých situacích může být úloha zablokovaná v nesprávném stavu. K tomu může dojít, zejména pokud se oprávnění účtu úložiště nemusela správně nastavit. Jedním ze způsobů, jak ověřit, jestli je export úspěšný, je zkontrolovat účet úložiště a zjistit, jestli jsou k dispozici odpovídající soubory kontejneru (tj. ndjson). Pokud nejsou k dispozici a nejsou spuštěny žádné jiné úlohy exportu, existuje možnost, že aktuální úloha bude zablokována ve špatném stavu. Úlohu exportu byste měli zrušit odesláním žádosti o zrušení a pak úlohu znovu zařadíte do fronty. Naším výchozím časem spuštění exportu ve špatném stavu je 10 minut, než se zastaví a přesune na novou úlohu, nebo zkuste export zopakovat. 

Rozhraní Azure API pro FHIR podporuje $export na následujících úrovních:
* [Systém](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Pacient](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Skupina pacientů *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) – Azure API pro FHIR exportuje všechny související prostředky, ale neexportují charakteristiky této skupiny: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

Při exportu dat se vytvoří samostatný soubor pro každý typ prostředku. Aby bylo zajištěno, že exportované soubory nebudou příliš velké. Vytvoříme nový soubor, jakmile bude velikost jednoho exportovaného souboru větší než 64 MB. Výsledkem je, že můžete získat více souborů pro každý typ prostředku, který se bude vyčíslovat (tedy pacient-1. ndjson, pacient 2. ndjson). 


> [!Note] 
> `Patient/$export` a `Group/[ID]/$export` můžou exportovat duplicitní prostředky, pokud je prostředek v prostoru více než jednoho prostředku nebo je ve více skupinách.

Kromě toho se při zrušení aktuální úlohy exportu kontroluje stav exportu prostřednictvím adresy URL vrácené hlavičkou umístění v rámci služby Řízení front.

### <a name="exporting-fhir-data-to-adls-gen2"></a>Export dat FHIR do ADLS Gen2

V současné době podporujeme $export pro ADLS Gen2 s povolenými účty úložiště s následujícím omezením:

- Uživatel nemůže využít výhod [hierarchických oborů názvů](../../storage/blobs/data-lake-storage-namespace.md), ale neexistuje způsob, jak cílit na export do konkrétního podadresáře v rámci kontejneru. Nabízíme možnost cílit jenom na konkrétní kontejner (kde vytvoříme novou složku pro každý export).
- Po dokončení exportu nikdy neexportujte do této složky nic, protože další exporty do stejného kontejneru budou uvnitř nově vytvořené složky.


## <a name="settings-and-parameters"></a>Nastavení a parametry

### <a name="headers"></a>Hlavičky
Existují dva povinné parametry hlaviček, které je třeba nastavit pro $export úlohy. Hodnoty jsou definovány pomocí [specifikace aktuální $export](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers).
* **Accept** -Application/fhir + JSON
* **Preferovat** – odpověď – asynchronní

### <a name="query-parameters"></a>Parametry dotazů
Rozhraní Azure API pro FHIR podporuje následující parametry dotazu. Všechny tyto parametry jsou volitelné:

|Parametr dotazu        | Definováno specifikací FHIR?    |  Description|
|------------------------|---|------------|
| \_outputFormat | Yes | V současné době podporuje tři hodnoty, které se mají Zarovnat ke specifikaci FHIR: Application/FHIR + ndjson, Application/ndjson nebo pouze ndjson. Všechny úlohy exportu budou vracet `ndjson` a předaná hodnota nemá žádný vliv na chování kódu. |
| \_doby | Yes | Umožňuje exportovat pouze prostředky, které byly od zadaného času změněny. |
| \_textový | Yes | Umožňuje určit typy prostředků, které budou zahrnuty. Například \_ typ = pacient by vrátil jenom prostředky pacienta.|
| \_typefilter | Yes | Chcete-li požádat o přesnější filtrování, můžete použít \_ TypeFilter spolu s \_ parametrem typu. Hodnota parametru _typeFilter je čárkami oddělený seznam dotazů FHIR, které dále omezují výsledky. |
| \_vnitřního | No |  Určuje kontejner v nakonfigurovaném účtu úložiště, kam se mají data exportovat. Pokud je určen kontejner, data budou exportována do tohoto kontejneru do nové složky s názvem. Pokud kontejner není zadán, bude exportován do nového kontejneru pomocí časového razítka a ID úlohy. |

> [!Note]
> Jako cíl pro $export operace se smějí registrovat jenom účty úložiště ve stejném předplatném, jako je pro Azure API pro FHIR.

## <a name="secure-export-to-azure-storage"></a>Zabezpečený export do Azure Storage

Rozhraní Azure API pro FHIR podporuje operaci zabezpečeného exportu. Jednou z možností, jak spustit zabezpečený export, je povolit pro přístup k účtu úložiště Azure konkrétní IP adresy přidružené k rozhraní Azure API pro FHIR. Konfigurace se liší v závislosti na tom, jestli je účet úložiště ve stejném nebo jiném umístění než Azure API pro FHIR.

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>Když je účet úložiště Azure v jiné oblasti

Z portálu vyberte **síť** účtu úložiště Azure. 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="Azure Storage nastavení sítě." lightbox="media/export-data/storage-networking.png":::
   
Vyberte **Vybrané sítě**. V části Brána firewall zadejte IP adresu do pole **Rozsah adres** . Přidejte rozsahy IP adres, které umožní přístup z Internetu nebo místních sítí. IP adresu najdete v následující tabulce pro oblast Azure, ve které se zřídí rozhraní Azure API pro FHIR.

|**Oblast Azure**         |**Veřejná IP adresa** |
|:----------------------|:-------------------|
| Austrálie – východ       | 20.53.44.80       |
| Střední Kanada       | 20.48.192.84      |
| USA – střed           | 52.182.208.31     |
| East US              | 20.62.128.148     |
| USA – východ 2            | 20.49.102.228     |
| Východní USA 2 EUAP       | 20.39.26.254      |
| Německo – sever        | 51.116.51.33      |
| Německo – středozápad | 51.116.146.216    |
| Japonsko – východ           | 20.191.160.26     |
| Jižní Korea – střed        | 20.41.69.51       |
| USA – středosever     | 20.49.114.188     |
| Severní Evropa         | 52.146.131.52     |
| Jižní Afrika – sever   | 102.133.220.197   |
| Středojižní USA     | 13.73.254.220     |
| Southeast Asia       | 23.98.108.42      |
| Švýcarsko – sever    | 51.107.60.95      |
| Spojené království – jih             | 51.104.30.170     |
| Spojené království – západ              | 51.137.164.94     |
| USA – středozápad      | 52.150.156.44     |
| West Europe          | 20.61.98.66       |
| Západní USA 2            | 40.64.135.77      |

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>Když je účet úložiště Azure ve stejné oblasti

Proces konfigurace je stejný, jak je uvedeno výše, s výjimkou, že se místo toho použije konkrétní rozsah IP adres ve formátu CIDR, 100.64.0.0/10. Důvodem, proč je nutné zadat rozsah IP adres, který zahrnuje 100.64.0.0 – 100.127.255.255, je, protože skutečná IP adresa používaná službou se liší, ale bude v rámci rozsahu pro každý požadavek $export.

> [!Note] 
> Je možné, že se místo toho dá použít privátní IP adresa v rozsahu 10.0.2.0/24. V takovém případě se operace $export nezdařila. Požadavek na $export můžete opakovat, ale není nijak zaručeno, aby se v dalším čase používala IP adresa v rozsahu 100.64.0.0/10. To je známé chování sítě podle návrhu. Alternativou je konfigurace účtu úložiště v jiné oblasti.
    
## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak exportovat FHIR prostředky pomocí příkazu $export. Další informace o tom, jak exportovat deidentifikovaná data, najdete v těchto tématech:
 
>[!div class="nextstepaction"]
>[Exportovat de identifikovaná data](de-identified-export.md)

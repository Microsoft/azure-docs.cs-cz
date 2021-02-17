---
title: Normalizace dat ve službě Azure Sentinel | Microsoft Docs
description: Tento článek vysvětluje, jak funkce Sentinel v Azure normalizuje data z mnoha různých zdrojů a podrobně popisuje schéma normalizace.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 35b3f4fe5703e9ce902553f992dfa44cea7db78f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570448"
---
# <a name="normalization-in-azure-sentinel"></a>Normalizace v Azure Sentinel

Tento článek popisuje normalizaci schématu dat ve službě Azure Sentinel a konkrétně schéma síťových připojení a relací, do kterých je odkaz zahrnutý.

## <a name="what-is-normalization"></a>Co je normalizace

Práce s různými datovými typy a tabulkami prezentují problémy. Je nutné se seznámit s mnoha různými typy dat a schématy, které musí napsat a používat jedinečnou sadu analytických pravidel, sešitů a loveckých dotazů pro každou, a to i pro ty, které sdílejí commonalities (například vztahující se k zařízením brány firewall). Korelace mezi různými datovými typy, které jsou nezbytné pro šetření a lov, je také obtížné. Funkce Sentinel Azure poskytuje bezproblémové prostředí pro zpracování dat z různých zdrojů v jednotných, normalizovaných zobrazeních.

**Common Information Model** Azure Sentinel se skládá ze tří aspektů:

- **Normalizovaná schémata** pokrývají společné sady předvídatelných typů událostí (tabulky), se kterými se snadno pracuje a sestavují sjednocené možnosti (například tabulka sítě). Schéma zahrnuje také normalizovanou konvenci sloupců a definice pro normalizaci hodnot a formátů (standardní reprezentace dat, jako jsou například IP adresy).

- **Analyzátory** mapují existující data různých typů na normalizované schéma. Podle modelu je možné data analyzovat do normalizovaného schématu v době dotazu (pomocí funkcí) nebo času příjmu. V současné době je podporována pouze analýza v době dotazu.

- **Obsah pro každé normalizované schéma** zahrnuje pravidla analýzy, interaktivní sešity, lovecké dotazy a další obsah.

### <a name="current-release"></a>Aktuální verze

V této verzi je ve verzi Public Preview k dispozici [normalizované schéma síťových připojení a relací](./normalization-schema.md) (v 1.0.0). Schéma popisuje síťová připojení nebo relace, například ty, které jsou protokolovány branami firewall, síťovými daty, NSG, NetFlow, proxy systémy a branami zabezpečení webu.  Vybrané analyzátory času dotazu a analytická pravidla jsou k dispozici společně se schématem a využívají je.

Schéma je aktuálně k dispozici pouze pomocí analyzátorů doby dotazu (viz část analyzátory).

Data můžete analyzovat pro další reprezentace a pomocí [názvového modelu entit OSSEM](https://ossemproject.com/cdm/entities/intro.html#) vytvořit sloupce, které budou konzistentní se stávajícími a budoucími normalizovanými tabulkami.

## <a name="normalized-schema-and-parsing"></a>Normalizované schéma a analýza

### <a name="how-our-normalized-schemas-are-defined"></a>Jak jsou definována naše normalizovaná schémata

Sentinel Azure je v souladu s [otevřeným zdrojovým modelem metadat událostí zabezpečení (OSSEM)](https://ossemproject.com/intro.html) a umožňuje předvídatelné entity korelace napříč normalizovanými tabulkami. OSSEM je projekt založený na komunitě, který se zaměřuje hlavně na dokumentaci a standardizaci protokolů událostí zabezpečení z nejrůznějších zdrojů dat a operačních systémů. Kromě toho projekt poskytuje model CIM (Common Information Model), který lze použít pro inženýry dat během normalizace dat a umožňuje tak analytikům zabezpečení zadávat dotazy a analyzovat data napříč různými zdroji dat.

[CIM OSSEM](https://ossemproject.com/cdm/intro.html) definuje sadu entit (například File, Host, IP, Process) a definuje sadu atributů pro každou takovou entitu. Kromě toho CIM definuje sadu tabulek (například tabulku [síťové relace](https://ossemproject.com/cdm/tables/network_session.html) ), které používají relevantní atributy z těchto entit a umožňují bezproblémové a předvídatelné korelaci. Všimněte si, že entity můžou být vnořené (například zdrojová entita může obsahovat soubor entity, která bude mít atribut Name).

Další informace o struktuře entit OSSEM najdete v [oficiálních referenčních](https://ossemproject.com/cdm/guidelines/entity_structure.html)informacích k OSSEM.

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>Jak jsou normalizovaná schémata implementovaná v Azure Sentinel

Při implementaci OSSEM CIM ve službě Azure Sentinel procházíme OSSEM reprezentace do Log Analytics tabulkové reprezentace, ať už je tato reprezentace předdefinovaná tabulkou, nebo byla vytvořená pomocí analyzátorů nebo funkcí v době dotazu, které mapují existující data na tuto reprezentaci. Pro tabulkovou reprezentaci je zřetězení názvů entit OSSEM a názvů atributů a jejich mapování souhrnných na jeden název sloupce. Například zdrojová entita obsahující entitu File obsahující entitu hash obsahující atribut MD5, bude implementována jako následující Log Analytics sloupec: SrcFileHashMd5. (OSSEM používá ve výchozím nastavení *snake_case* , zatímco služba Azure Sentinel a Log Analytics používá *PascalCase*. V OSSEM by byl sloupec src_file_hash_md5.)

V implementaci služby Azure Sentinel můžou existovat další vlastní pole z důvodu Log Analytics požadavků na platformu a případů použití, které jsou specifické pro zákazníky s Sentinel Azure.

### <a name="schema-reference"></a>Odkaz na schéma

Další informace najdete v [referenčním dokumentu schématu](./normalization-schema.md)a v oficiální [dokumentaci k projektu OSSEM](https://ossemproject.com/cdm/intro.html).

Odkaz na schéma obsahuje taky hodnoty a standardizaci formátu. Zdrojová pole, původní nebo analyzovaná, nemusí být ve standardním formátu, ani používat standardní seznam hodnot schématu, a proto je potřeba je převést na standardní reprezentaci schématu, aby bylo možné je plně normalizovat.

## <a name="parsers"></a>Analyzátorů

- [Co je analýza](#what-is-parsing)
- [Použití analyzátorů času dotazů](#using-query-time-parsers)

### <a name="what-is-parsing"></a>Co je analýza

K dispozici je základní sada definovaných normalizovaných tabulek, bude nutné transformovat (analyzovat/mapovat) data do těchto tabulek. To znamená, že v normalizovaném schématu budete z jeho nezpracovaného formuláře extrahovat konkrétní data do známých sloupců. K analýze v Azure Sentinel dojde v **době dotazu** . analyzátory se vytvářejí jako Log Analytics uživatelské funkce (pomocí KQL jazyka Kusto), které transformují data v existujících tabulkách (například CommonSecurityLog, Custom log Tables, syslog) do normalizovaného schématu tabulek.

Druhý druh analýzy, který se ještě v Azure Sentinelu nepodporuje, je **čas** příjmu, který umožňuje shromažďovat data přímo do normalizovaných tabulek, protože je ingestují z jejích zdrojů dat. Analýza času příjmu poskytuje vyšší výkon, protože datový model se dotazuje přímo bez nutnosti používat funkce.

### <a name="using-query-time-parsers"></a>Použití analyzátorů času dotazů

- [Instalace analyzátoru](#installing-a-parser)
- [Použití analyzátorů](#using-the-parsers)
- [Přizpůsobení analyzátorů](#customizing-parsers)

#### <a name="installing-a-parser"></a>Instalace analyzátoru

Dostupné analyzátory času dotazů jsou k dispozici v [oficiálním úložišti GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0))Azure Sentinel. Každý analyzátor má verzi, aby mohli zákazníci snadno používat a monitorovat budoucí aktualizace. Instalace analyzátoru:

1. Zkopírujte příslušný obsah analyzátoru z každého relevantního souboru KQL ve výše uvedeném odkazu na GitHub do schránky

1. Na portálu Sentinel Azure otevřete stránku protokoly a vložte obsah souboru KQL do obrazovky protokoly a klikněte na **Uložit**.

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="Nainstalovat nový analyzátor":::

1. V dialogovém okně Uložit vyplňte pole následujícím způsobem:
    1. **Název**: doporučuje se použít stejnou hodnotu použitou v poli **alias funkce** (v příkladu výše *CheckPoint_Network_NormalizedParser*).
    
    1. **Uložit jako**: Select – **funkce**

    1. **Alias funkce**: měla by být pojmenována v následujících konvencích vytváření názvů – *PRODUCT_Network_NormalizedParser* (v předchozím příkladu *CheckPoint_Network_NormalizedParser*).

    1. **Kategorie**: můžete vybrat existující kategorii nebo vytvořit novou kategorii (například *NormalizedNetworkSessionsParsers*).
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="Uložit analyzátor":::

Aby bylo možné správně použít analyzátory, je nutné také nainstalovat prázdný analyzátor schématu sítě (který vytvoří prázdné tabulkové zobrazení všech polí schématu síťové relace) a meta-analyzátor sítě (který sjednotí všechny povolené analyzátory k vytvoření jednoho zobrazení dat z různých zdrojů v síťovém schématu). Instalace těchto dvou analyzátorů se provádí podobným způsobem jako u výše uvedených kroků.

Při ukládání funkce dotazu může být nutné zavřít Průzkumníka dotazů a znovu ho otevřít, aby se projevila nová funkce.

#### <a name="using-the-parsers"></a>Použití analyzátorů

Jakmile je povoleno, můžete použít meta-parser k dotazování sjednoceného zobrazení na všechny aktuálně povolené analyzátory. Provedete to tak, že přejdete na stránku protokoly Sentinel Azure a zadáte dotaz meta-parser:

:::image type="content" source="./media/normalization/query-parser.png" alt-text="Dotazování analyzátoru":::
 
K meta analyzátoru nebo k jednotlivým analyzátorům můžete přistupovat pomocí Průzkumníku dotazů na stránce protokoly kliknutím na Průzkumník dotazů:

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="Průzkumník dotazů":::

V pravém podokně rozbalte část "uložené dotazy" a vyhledejte složku ' NormalizedNetworkParsers ' (nebo název kategorie, který jste zvolili při vytváření analyzátorů):

:::image type="content" source="./media/normalization/find-parser.png" alt-text="Najít analyzátor":::

Můžete kliknout na každý jednotlivý analyzátor a zobrazit základní funkci, kterou používá, a spustit ji (nebo k ní přistupovat přímo pomocí aliasu, jak je popsáno výše). Všimněte si, že některé analyzátory mohou zachovat původní pole vedle sebe do normalizovaných polí pro usnadnění práce. To lze snadno upravit v dotazu analyzátoru.

> [!TIP]
> Uložené funkce můžete použít místo tabulek Sentinel Azure v jakémkoli dotazu, včetně loveckých a zjišťovacích dotazů. Další informace naleznete v tématu:
>
> - [Normalizace dat v Azure Sentinel](normalization.md#parsers)
> - [Analyzovat text v protokolech Azure Monitor](/azure/azure-monitor/log-query/parse-text)
>
#### <a name="customizing-parsers"></a>Přizpůsobení analyzátorů

Výše uvedený postup můžete opakovat (hledání analyzátoru v Průzkumníku dotazů), kliknout na příslušný analyzátor a zobrazit jeho implementaci funkce.
Můžete se například rozhodnout upravit meta analyzátor a přidat nebo odebrat jednotlivé analyzátory.

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="Přizpůsobení analyzátoru":::
 
Po změně funkce klikněte znovu na Uložit a použijte stejný název, alias a kategorii. Otevře se dialogové okno pro přepsání – stiskněte OK:

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="Opravdu":::

#### <a name="additional-information"></a>Další informace

JSON, XML a CSV jsou zvláště užitečné pro analýzu v době dotazu. Technologie Azure Sentinel obsahuje integrované funkce analýzy pro JSON, XML a CSV a také nástroj pro analýzu JSON.  Další informace najdete v tématu [použití polí JSON ve službě Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747) (blog). 

Přečtěte si další informace o [uložených dotazech](../azure-monitor/logs/example-queries.md) (implementaci analyzátorů času dotazů) v Log Analytics.


## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli o schématu normalizace Azure Sentinel. Referenční schéma samotného schématu najdete v tématu [referenční informace schématu normalizace dat služby Azure Sentinel](./normalization-schema.md).

* [Blog Sentinel Azure](https://aka.ms/azuresentinelblog). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
---
title: Informace o nejnovějších verzích azure guest os | Dokumenty společnosti Microsoft
description: Nejnovější zprávy o vydání a kompatibilita sady SDK pro hostovaný operační systém Azure Cloud Services.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 3/26/2020
ms.author: raiye
ms.openlocfilehash: f265faef4e6520f5c74fce9a13b3d81118707025
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371126"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Verze azure hostovaného operačního systému a matice kompatibility sady SDK
Poskytuje aktuální informace o nejnovějších verzích hostovaného operačního systému Azure pro cloudové služby. Tyto informace vám pomohou naplánovat cestu upgradu před zakázáním hostovaného operačního systému. Pokud nakonfigurujete své role tak, aby používaly *automatické* aktualizace hostovaného operačního systému, jak je popsáno v [nastavení aktualizace hostovaného operačního systému Azure][Azure Guest OS Update Settings], není důležité, abyste si tuto stránku přečetli.

> [!IMPORTANT]
> Tato stránka se vztahuje na webové a pracovní role cloudových služeb, které běží nad hostem. **Nevztahuje se** na virtuální počítače IaaS.
>
>


> [!TIP]
>  Přihlaste se k odběru [informačního kanálu RSS aktualizace hostovaného operačního systému] a získejte co nejvčasnější oznámení o všech změnách hostovaného operačního systému.
>
>

> [!IMPORTANT]
> Na webu Azure Portal budou podporované a dostupné jenom 2 verze hostovaného operačního systému.
>
>

Nejste si jisti, jak aktualizovat hostovaný operační systém? Podívej se na [tohle.][cloud updates]

## <a name="news-updates"></a>Aktuality

###### <a name="march-5-2020"></a>**5. března 2020**
Únor Host OS vydala. 

###### <a name="january-24-2020"></a>**24. ledna 2020**
Lednový host os vydala. 

###### <a name="january-8-2020"></a>**8. ledna 2020**
Prosincový hostovce os vydala.

###### <a name="december-5-2019"></a>**5. prosince 2019**
Listopadový hostovce os vydala.

###### <a name="november-1-2019"></a>**1. listopadu 2019**
Říjnový host os vydala.

###### <a name="october-7-2019"></a>**7. října 2019**
Září Guest OS vydala.

###### <a name="september-4-2019"></a>**4. září 2019 September 4, 2019**
Srpna Guest OS vydala.

###### <a name="july-26-2019"></a>**26. července 2019July 26, 2019**
Červencový host os vydala.

###### <a name="july-8-2019"></a>**8. července 2019**
Června Guest OS vydala.

###### <a name="june-6-2019"></a>**6. června 2019**
May Guest OS vydala.

###### <a name="may-7-2019"></a>**7. května 2019**
Dubna Guest OS vydala.

###### <a name="march-26-2019"></a>**26. března 2019**
Března Guest OS vydala.

###### <a name="march-12-2019"></a>**12. března 2019**
Únor Host OS vydala.

###### <a name="february-5-2019"></a>**5. února 2019**
Lednový host os vydala.

###### <a name="january-24-2019"></a>**24. ledna 2019 January 24, 2019**
Family 6 Guest OS (Windows Server 2019) byl vydán.

###### <a name="january-7-2019"></a>**7. ledna 2019**
Prosincový hostovce os vydala.

###### <a name="december-14-2018"></a>**14. prosince 2018December 14, 2018**
Listopadový hostovce os vydala.

###### <a name="november-8-2018"></a>**8. listopadu 2018**
Říjnový host os vydala.

###### <a name="october-12-2018"></a>**12. října 2018October 12, 2018**
Září Guest OS vydala.

## <a name="releases"></a>Vydání

## <a name="family-6-releases"></a>Rodina 6 vydání
**Windows Server 2019**

Nainstalována rozhraní .NET Framework: 3.5, 4.7.2

> [!NOTE]
> Sada Windows Azure SDK pro rozhraní .NET - 3.0 si můžete stáhnout [zde][Windows Azure SDK].
>
>Kroky instalace:
>1. Odinstalujte všechny starší verze microsoftAzureAuthoringTools*.msi
>2. Instalace [sady Azure SDK pro rozhraní .NET - 3.0][Windows Azure SDK]
>3. Restartujte počítač
>4. Vytvoření nového projektu cloudové služby a přidání jedné role pracovního procesu
>5. Změna řady operačních služeb na 6 a sestavení balíčku
>6. Nasazení balíčku do Azure pomocí portálu Azure nebo Visual Studia
>
>Verze Host OS Family 6 vynucuje TLS 1.2 explicitním zakázáním TLS 1.0 a 1.1 a definováním konkrétní sady šifrovacích sad. Další informace naleznete [v]


| Konfigurační řetězec | Datum vydání | Zakázat datum |
| --- | --- | --- |
|  WA-HOST-OS-6.16_202002-01  |  5. března 2020  |  Příspěvek 6.18  |  
|  WA-HOST-OS-6.15_202001-01  |  24. ledna 2020  |  Příspěvek 6.17  |  
|~~WA-HOST-OS-6.14_201912-01~~| 8. ledna 2020 | 5. března 2020 |  
|~~WA-HOST-OS-6.13_201911-01~~| 5. prosince 2019 | 24. ledna 2020 |  
|~~WA-HOST-OS-6.12_201910-01~~| 1. listopadu 2019 | 8. ledna 2020 |  
|~~WA-HOST-OS-6.11_201909-01~~| 7. října 2019 | 5. prosince 2019 |  
|~~WA-HOST-OS-6.10_201908-01~~| 4. srpna 2019August 4, 2019 | 1. listopadu 2019  |  
|~~WA-GUEST-OS-6.9_201907-0~~|26. července 2019July 26, 2019 | 7. října 2019 |
|~~WA-HOST-OS-6.8_201906-01~~|8. července 2019 |4. srpna 2019August 4, 2019 |
|~~WA-HOST-OS-6.7_201905-01~~ |6. června 2019 |26. července 2019July 26, 2019 |
|~~WA-HOST-OS-6.6_201904-01~~ |7. května 2019 |8. července 2019 |
|~~WA-HOST-OS-6.5_201903-01~~ |26. března 2019 |6. června 2019 |
|~~WA-HOST-OS-6.4_201902-01~~ |12. března 2019 |7. května 2019 |
|~~WA-HOST-OS-6.3_201901-01~~ |5. února 2019 |26. března 2019 |
|~~WA-HOST-OS-6.2_201812-01~~ |24. ledna 2019 January 24, 2019 |12. března 2019 |
|~~WA-HOST-OS-6.1_201811-01~~ |24. ledna 2019 January 24, 2019 |5. února 2019 |

## <a name="family-5-releases"></a>Rodina 5 vydání
**Windows Server 2016**

Nainstalovaný rozhraní .NET Framework: 3.5, 4.6

> [!NOTE]
> Heslo RDP pro rodinu operačních sů 5 musí mít minimálně 10 znaků.
>


| Konfigurační řetězec | Datum vydání | Zakázat datum |
| --- | --- | --- |
|  WA-HOST-OS-5.40_202002-01  |  5. března 2020  |  Příspěvek 5.42  |  
|  WA-HOST-OS-5.39_202001-01  |  24. ledna 2020  |  Příspěvek 5.41  |  
|~~WA-HOST-OS-5.38_201912-01~~| 8. ledna 2020 | 5. března 2020 |  
|~~WA-GUEST-OS-5.37_201911-01~~| 5. prosince 2019 | 24. ledna 2020 |  
|~~WA-GUEST-OS-5.36_201910-01~~| 1. listopadu 2019 | 8. ledna 2020 |  
|~~WA-GUEST-OS-5.35_201909-01~~| 7. října 2019 | 5. prosince 2019 |  
|~~WA-GUEST-OS-5.34_201908-01~~|  4. srpna 2019August 4, 2019  | 1. listopadu 2019 |  
|~~WA-GUEST-OS-5.33_201907-01~~| 26. července 2019July 26, 2019 | 7. října 2019 |  
|~~WA-HOST-OS-5.32_201906-01~~|8. července 2019 |4. srpna 2019August 4, 2019 |
|~~WA-GUEST-OS-5.31_201905-01~~ |6. června 2019 |26. července 2019July 26, 2019 |
|~~WA-HOST-OS-5.30_201904-01~~ |7. května 2019 |8. července 2019 |
|~~WA-HOST-OS-5.29_201903-01~~ |26. března 2019 |6. června 2019 |
|~~WA-HOST-OS-5.28_201902-01~~ |12. března 2019 |7. května 2019 |
|~~WA-GUEST-OS-5.27_201901-01~~ |5. února 2019 |26. března 2019 |
|~~WA-HOST-OS-5.26_201812-01~~ |7. ledna 2019 |12. března 2019 |
|~~WA-GUEST-OS-5.25_201811-01~~ |14. prosince 2018December 14, 2018 |5. února 2019 |
|~~WA-HOST-OS-5.24_201810-01~~ |8. listopadu 2018 |7. ledna 2019 |
|~~WA-HOST-OS-5.23_201809-01~~ |12. října 2018October 12, 2018 |14. prosince 2018December 14, 2018 |

## <a name="family-4-releases"></a>Rodina 4 vydání
**Windows Server 2012 R2**

Nainstalována rozhraní .NET Framework: 3.5, 4.5.1, 4.5.2

| Konfigurační řetězec | Datum vydání | Zakázat datum |
| --- | --- | --- |
|  WA-HOST-OS-4.75_202002-01  |  5. března 2020  |  Příspěvek 4,77  |  
|  WA-HOST-OS-4.74_202001-01  |  24. ledna 2020  |  Příspěvek 4,76  |  
|~~WA-HOST-OS-4.73_201912-01~~| 8. ledna 2020 | 5. března 2020 |  
|~~WA-HOST-OS-4.72_201911-01~~| 5. prosince 2019 | 24. ledna 2020 |  
|~~WA-HOST-OS-4.71_201910-01~~| 1. listopadu 2019 | 8. ledna 2020 |  
|~~WA-GUEST-OS-4.70_201909-01~~| 7. října 2019 | 5. prosince 2019 |  
|~~WA-GUEST-OS-4.69_201908-01~~| 4. srpna 2019August 4, 2019 | 1. listopadu 2019 |  
|~~WA-GUEST-OS-4.68_201907-01~~| 26. července 2019July 26, 2019  | 7. října 2019 |
|~~WA-HOST-OS-4.67_201906-01~~| 8. července 2019 |4. srpna 2019August 4, 2019 |
|~~WA-GUEST-OS-4.66_201905-01~~ |6. června 2019 |26. července 2019July 26, 2019 |
|~~WA-HOST-OS-4.65_201904-01~~ |7. května 2019 |8. července 2019 |
|~~WA-HOST-OS-4.64_201903-01~~ |26. března 2019 |6. června 2019 |
|~~WA-HOST-OS-4.63_201902-01~~ |12. března 2019 |7. května 2019 |
|~~WA-HOST-OS-4.62_201901-01~~ |5. února 2019 |26. března 2019 |
|~~WA-HOST-OS-4.61_201812-01~~ |7. ledna 2019 |12. března 2019 |
|~~WA-HOST-OS-4.60_201811-01~~ |14. prosince 2018December 14, 2018 |5. února 2019 |
|~~WA-HOST-OS-4.59_201810-01~~ |8. listopadu 2018 |7. ledna 2019 |
|~~WA-GUEST-OS-4.58_201809-01~~ |12. října 2018October 12, 2018 |14. prosince 2018December 14, 2018 |

## <a name="family-3-releases"></a>Rodina 3 vydání
**Windows Server 2012**

Nainstalovaný rozhraní .NET Framework: 3.5, 4.5

| Konfigurační řetězec | Datum vydání | Zakázat datum |
| --- | --- | --- |
|  WA-HOST-OS-3.82_202002-01  |  5. března 2020  |  Příspěvek 3.84  |  
|  WA-HOST-OS-3.81_202001-01  |  24. ledna 2020  |  Příspěvek 3.83  |  
|~~WA-HOST-OS-3.80_201912-01~~| 8. ledna 2020 | 5. března 2020 |  
|~~WA-GUEST-OS-3.79_201911-01~~| 5. prosince 2019 | 24. ledna 2020 |  
|~~WA-GUEST-OS-3.78_201910-01~~| 1. listopadu 2019 | 8. ledna 2020 |  
|~~WA-GUEST-OS-3.77_201909-01~~| 7. října 2019 | 5. prosince 2019 |  
|~~WA-GUEST-OS-3.76_201908-01~~|  4. srpna 2019August 4, 2019  |  1. listopadu 2019  |  
|~~WA-GUEST-OS-3.75_201907-01~~| 26. července 2019July 26, 2019 | 7. října 2019 |
|~~WA-GUEST-OS-3.74_201906-01~~| 8. července 2019 |4. srpna 2019August 4, 2019 |
|~~WA-GUEST-OS-3.73_201905-01~~ |6. června 2019 |26. července 2019July 26, 2019 |
|~~WA-HOST-OS-3.72_201904-01~~ |7. května 2019 |8. července 2019 |
|~~WA-HOST-OS-3.71_201903-01~~ |26. března 2019 |6. června 2019 |
|~~WA-GUEST-OS-3.70_201902-01~~ |12. března 2019 |7. května 2019 |
|~~WA-HOST-OS-3.69_201901-01~~ |5. února 2019 |26. března 2019 |
|~~WA-HOST-OS-3.68_201812-01~~ |7. ledna 2019 |12. března 2019 |
|~~WA-HOST-OS-3.67_201811-01~~ |14. prosince 2018December 14, 2018 |5. února 2019 |
|~~WA-GUEST-OS-3.66_201810-01~~ |8. listopadu 2018 |7. ledna 2019 |
|~~WA-GUEST-OS-3.65_201809-01~~ |12. října 2018October 12, 2018 |14. prosince 2018December 14, 2018 |

## <a name="family-2-releases"></a>Verze řady 2
**Windows Server 2008 R2 SP1**

Nainstalovaný rozhraní .NET Framework: 3.5 (včetně 2.0 a 3.0), 4.5

| Konfigurační řetězec | Datum vydání | Zakázat datum |
| --- | --- | --- |
|  WA-HOST-OS-2.95_202002-01  |  5. března 2020  |  Příspěvek 2,97  |  
|  WA-HOST-OS-2.94_202001-01 WA-GUEST-OS-2.94_202001-01 WA-GUEST-OS-2.94_202001-01 WA-  |  24. ledna 2020  |  Příspěvek 2.96  |  
|~~WA-HOST-OS-2.93_201912-01~~| 8. ledna 2020 | 5. března 2020 |  
|~~WA-HOST-OS-2.92_201911-01~~| 5. prosince 2019 | 24. ledna 2020 |  
|~~WA-HOST-OS-2.91_201910-01~~| 1. listopadu 2019 | 8. ledna 2020 |  
|~~WA-GUEST-OS-2.90_201909-01~~| 7. října 2019 | 5. prosince 2019 |  
|~~WA-GUEST-OS-2.89_201908-01~~| 4. srpna 2019August 4, 2019 | 1. listopadu 2019 |  
|~~WA-GUEST-OS-2.88_201907-01~~| 26. července 2019July 26, 2019 | 7. října 2019 |
|~~WA-HOST-OS-2.87_201906-01~~|8. července 2019 | 4. srpna 2019August 4, 2019 |
|~~WA-GUEST-OS-2.86_201905-01~~ |6. června 2019 |26. července 2019July 26, 2019 |
|~~WA-GUEST-OS-2.85_201904-01~~ |7. května 2019 |8. července 2019 |
|~~WA-HOST-OS-2.84_201903-01~~ |26. března 2019 |6. června 2019 |
|~~WA-HOST-OS-2.83_201902-01~~ |12. března 2019 |7. května 2019 |
|~~WA-HOST-OS-2.82_201901-01~~ |5. února 2019 |26. března 2019 |
|~~WA-HOST-OS-2.81_201812-01~~ |7. ledna 2019 |12. března 2019 |
|~~WA-HOST-OS-2.80_201811-01~~ |14. prosince 2018December 14, 2018 |5. února 2019 |
|~~WA-GUEST-OS-2.79_201810-01~~ |8. listopadu 2018 |7. ledna 2019 |
|~~WA-GUEST-OS-2.78_201809-01~~ |12. října 2018October 12, 2018 |14. prosince 2018December 14, 2018 |

## <a name="msrc-patch-updates"></a>Aktualizace oprav MSRC
Seznam oprav, které jsou součástí každé měsíční verze hostovaného operačního systému, je k dispozici [zde][patches].

## <a name="sdk-support"></a>Podpora SDK
I když [zásady vyřazení pro Azure SDK][retire policy sdk] označuje, že jsou podporovány pouze verze nad 2.2, konkrétní rodiny hostovaného operačního systému umožňují používat starší verze. Vždy byste měli používat nejnovější podporovanou sadu SDK.

| Host OS rodina | Kompatibilní verze sady SDK |
| --- | --- |
| 6 |Verze 2.9.6+ |
| 5 |Verze 2.9.5.1+ |
| 4 |Verze 2.1+ |
| 3 |Verze 1.8+ |
| 2 |Verze 1.3+ |
| 1 |Verze 1.0+ |

## <a name="guest-os-release-information"></a>Informace o vydání hostovaného operačního systému
Existují tři data, která jsou důležitá pro verze hostovaného operačního systému: datum **vydání,** **zakázané** datum a datum **vypršení platnosti.** Hostovaný operační systém je považován za dostupný, pokud je na portálu a může být vybrán jako cílový hostovaný operační systém. Když hostovaný operační soubor dosáhne **zakázané** datum, je odebrán z Azure. Jakékoli cílení cloudové služby na hostovaný operační systém však bude nadále fungovat jako normální.

Okno mezi **zakázaným** datem a datem **vypršení platnosti** poskytuje vyrovnávací paměť pro snadný přechod z jednoho hostovaného operačního systému na novější. Pokud používáte *automatické* jako hostovaný operační systém, budete vždy na nejnovější verzi a nemusíte se starat o to, že vyprší.

Po uplynutí doby **platnosti** bude všechny cloudové služby, které stále používají tento hostovaný operační systém, zastaveny, odstraněny nebo nuceny k upgradu. Můžete si přečíst více o důchodové politiky [zde][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Vysvětlení rodinné verze hosta operačního systému
Rodiny hostovaného operačního systému jsou založeny na vydaných verzích systému Microsoft Windows Server. Hostovaný operační systém, na který běží cloudové služby Azure. Každý hostovaný operační systém má rodinu, verzi a číslo vydání.

* **Host OS rodina**  
  Verze operačního systému Windows Server, na které je založen hostovaný operační systém. Například *rodina 3* je založena na Systému Windows Server 2012.
* **Verze hostovaného operačního systému**  
  Specifické pro bitovou kopii rodiny hostovaného operačního systému a příslušné opravy [Microsoft Security Response Center (MSRC),][msrc] které jsou k dispozici k datu vytvoření nové verze hostovaného operačního systému. Nemusí být zahrnuty všechny záplaty.

    Čísla začínají na 0 a přírůstek o 1 pokaždé, když je přidána nová sada aktualizací. Koncové nuly jsou zobrazeny pouze v případě, že je to důležité. To znamená, že verze 2.10 je jiná, mnohem novější verze než verze 2.1.
* **Verze hostovaného operačního systému**  
  Opětovné vydání verze hostovaného operačního systému. K opětovnému vydání dojde, pokud společnost Microsoft zjistí problémy během testování; vyžadující změny. Nejnovější verze vždy nahrazuje všechny předchozí verze, veřejné nebo ne. Portál Azure umožní uživatelům pouze vybrat nejnovější verzi pro danou verzi. Nasazení spuštěná v předchozí verzi obvykle nejsou vynucována upgradem v závislosti na závažnosti chyby.

V níže uvedeném příkladu je 2 rodina, 12 je verze a "rel2" je vydání.

**Host OS vydání** - 2,12 rel2

**Konfigurační řetězec pro tuto verzi** - WA-GUEST-OS-2.12_201208-02

Konfigurační řetězec pro hostovaný operační systém má stejné informace vložené v něm, spolu s datem zobrazujícím, které opravy MSRC byly považovány za pro tuto verzi. V tomto příkladu byly pro zahrnutí považovány za opravy MSRC vyrobené pro systém Windows Server 2008 R2 až do srpna 2012 včetně. Zahrnuty jsou pouze opravy, které se konkrétně vztahují na tuto verzi systému Windows Server. Pokud se například oprava MSRC vztahuje na sadu Microsoft Office, nebude zahrnuta, protože tento produkt není součástí základní bitové kopie systému Windows Server.

## <a name="guest-os-system-update-process"></a>Proces aktualizace systému hostovaného operačního systému
Tato stránka obsahuje informace o nadcházejících verzích hostovaného operačního systému. Zákazníci uvedli, že chtějí vědět, kdy dojde k vydání, protože jejich role cloudové služby se restartují, pokud jsou nastaveny na "Automatickou" aktualizaci. Verze hostovaného operačního systému se obvykle vyskytují 2-3 týdny po vydání aktualizace MSRC, ke kterému dochází druhé úterý v měsíci. Nové verze zahrnují všechny příslušné opravy MSRC pro každou rodinu hostovaného operačního systému.

Microsoft Azure neustále vydává aktualizace. Hostovaný operační systém je pouze jedna taková aktualizace v kanálu. Uvolnění může být ovlivněno mnoha faktory příliš mnoho na to, aby zde. Kromě toho Azure běží na doslova stovky tisíc počítačů. To znamená, že není možné uvést přesné datum a čas, kdy se vaše role restartuje. Pracujeme na plánu na omezení nebo čas restartuje.

Když se publikuje nová verze hostovaného operačního systému, může chvíli trvat, než se plně rozšíří v azure. Jako služby jsou aktualizovány na nový hostovaný operační systém, jsou restartovány ctít aktualizační domény. Služby nastavené na použití "Automatických" aktualizací získají nejprve vydání. Po aktualizaci se na webu Azure Portal zobrazí nová verze hostovaného operačního systému uvedená pro vaši službu. Během tohoto období může dojít k opětovnému uvolnění. Některé verze mohou být nasazeny po delší dobu a automatické restartování upgradu nemusí nastat po mnoho týdnů po oficiálním datu vydání. Jakmile je hostovaný operační systém k dispozici, můžete explicitně zvolit tuto verzi z portálu nebo v konfiguračním souboru.

Pro velké množství cenných informací o restartování a odkazy na další informace technické podrobnosti o hosta a hostitele aktualizace operačního systému, naleznete v blogu MSDN s názvem [Role Instance restartuje z důvodu upgradů operačního systému][restarts].

Pokud ručně aktualizujete hostovaný operační systém, další informace naleznete v [zásadách vyřazení hostovaného operačního systému.][retirepolicy]

## <a name="guest-os-supportability-and-retirement-policy"></a>Zásady podpory hostovaného operačního systému a vyřazení
Zásady podpory a vyřazení hostovaného operačního systému je [vysvětleno zde][retirepolicy].

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Host oS Aktualizace RSS feed]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
[Více]: https://docs.microsoft.com/azure/cloud-services/applications-dont-support-tls-1-2  

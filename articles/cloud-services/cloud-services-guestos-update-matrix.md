---
title: Další informace o nejnovějších verzích operačního systému hosta Azure | Microsoft Docs
description: Nejnovější verze informace a kompatibility sady SDK pro Azure Cloud Services hostovaného operačního systému.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 3/19/2018
ms.author: raiye
ms.openlocfilehash: 0456bf8d8cdd84078d12c3fc0f48d16b0370cddd
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure verze hostovaného operačního systému a kompatibilních sad SDK
Poskytuje že aktuální informace o nejnovější hostovaného operačního systému Azure verze pro cloudové služby. Tyto informace vám pomůžou naplánovat způsob upgradu než hostovaného operačního systému je zakázáno. Pokud nakonfigurujete použití role *automatické* aktualizace hostovaného operačního systému, jak je popsáno v [nastavení aktualizace operačního systému hosta Azure][Azure Guest OS Update Settings], není důležité, abyste si přečetli tuto stránku.

> [!IMPORTANT]
> Tato stránka se vztahuje na webových a pracovních cloudové služby rolí, které běží nad hostovaného operačního systému. Provede **nelze použít** pro virtuální počítače IaaS.
>
>


> [!TIP]
>  Přihlášení k odběru [hostovaného operačního systému aktualizace informačního kanálu RSS,] pro příjem nejvíce včas oznámení na všechny změny hostovaného operačního systému.
>
>

> [!IMPORTANT]
> Od listopadu zavedení, pouze nejnovější 2 verze hostovaného operačního systému bude podporované a k dispozici na portálu Azure.
>
>

Nejste si jistí, o jaké hostovaného operačního systému je nebo jak hostovaného operačního systému uvolní pracovní? Čtení [to](#how-it-works) části.

## <a name="news-updates"></a>Nejnovější zprávy
###### <a name="march-19-2018"></a>**19. března 2018**
Vydala února hostovaného operačního systému.

###### <a name="january-29-2018"></a>**29. ledna 2018**
Leden hostovaného operačního systému byla vydána pro operační systém řady 2 (WA-hosta-operačního systému-2.70_201801-01) & 3 (WA-hosta-operačního systému-3.57_201801-01)

###### <a name="january-4-2018"></a>**4. ledna 2018**
Leden hostovaného operačního systému byla vydána pro operační systém řady 4 (WA-hosta-operačního systému-4.50_201801-01) & 5 (WA-hosta-operačního systému-5.15_201801-01) a obsahuje opravy důležité zabezpečení.  

###### <a name="january-4-2018"></a>**4. ledna 2018**
Vydala prosinec hostovaného operačního systému.

###### <a name="december-14-2017"></a>**14. prosince 2017**
Vydala listopadu hostovaného operačního systému.

###### <a name="november-8-2017"></a>**8 listopadu 2017**
Vydala říjen hostovaného operačního systému.

###### <a name="october-6-2017"></a>**6 říjen 2017**
Vydala září hostovaného operačního systému. Pro tuto verzi systému Windows Server 2016 září netfx3 ve výchozím nastavení zapnutá. Zákazníci měli přidat ' dism / online Feature /featurename:netfx3 se v jejich OnStart, pokud jejich pracovní postup vyžaduje, aby se spouštěly 2.x aplikace .NET s 4.x runtime nebo v případě, že spustil 2.x aplikace .NET, zpracovává chybu a pak se spustil 4.x aplikace .NET.

###### <a name="september-14-2017"></a>**14 září 2017**
Zavedení září hostovaného operačního systému se od září 14 a má předpokládané verzi říjen 9.

###### <a name="august-24-2017"></a>**24 srpen 2017**
Vydala srpen hostovaného operačního systému.

###### <a name="august-3-2017"></a>**3. srpna 2017**
Vydala července hostovaného operačního systému.

###### <a name="july-19-2017"></a>**19. července 2017**
Zavedení července hostovaného operačního systému se spouští července 19 a má předpokládané verzi srpen 8.


## <a name="releases"></a>Verze
## <a name="family-5-releases"></a>Uvolní rodiny 5
**Windows Server 2016**

Nainstalované rozhraní .NET framework: 4.0, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1, 4.6.2

> [!NOTE]
> Data s * jsou mohou podléhat změnám.
>
> Heslo RDP pro operační systém řady 5 musí být minimálně 10 znaků.
>

| Konfigurační řetězec | Datum vydání | Zakázat datum | Vypršela platnost datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-5.16_201802-01 |12. březen 2018 |POST 5.18 |Bude doplněno |
| WA-GUEST-OS-5.15_201801-01 |4. ledna 2018 |POST 5.17 |Bude doplněno |
|~~WA-GUEST-OS-5.14_201712-01~~ |4. ledna 2018 |12. březen 2018 |Bude doplněno |
|~~WA-GUEST-OS-5.13_201711-01~~ |14. prosince 2017 |4. ledna 2018|Bude doplněno |
|~~WA-GUEST-OS-5.12_201710-02~~ |8 listopadu 2017 |4. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-5.11_201709-01~~ |6 říjen 2017 |14. prosince 2017 |Bude doplněno |
|~~WA-GUEST-OS-5.10_201708-01~~ |24 srpen 2017 |14. prosince 2017 |Bude doplněno |
|~~WA-GUEST-OS-5.9_201707-01~~ |3. srpna 2017 |8 listopadu 2017 |Bude doplněno |
|~~WA-GUEST-OS-5.8_201706-01~~ |7. července 2017 |6 říjen 2017 |Bude doplněno |
|~~WA-GUEST-OS-5.7_201705-01~~ |5 června 2017 |24 srpen 2017 |Bude doplněno |
|~~WA-GUEST-OS-5.6_201704-01~~ |9 může 2017 |3. srpna 2017 |Bude doplněno |
|~~WA-GUEST-OS-5.5_201703-01~~ |10. dubna 2017 |7. července 2017 |Bude doplněno |
|~~WA-GUEST-OS-5.4_201612-01~~ |10. ledna 2017 |5 června 2017|Bude doplněno |

## <a name="family-4-releases"></a>Uvolní rodiny 4
**Windows Server 2012 R2**

Nainstalované rozhraní .NET framework: 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Data s * se mohou změnit
>
>

| Konfigurační řetězec | Datum vydání | Zakázat datum | Vypršela platnost datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-4.51_201802-01 |12. březen 2018 |POST 4,53 |Bude doplněno |
| WA-GUEST-OS-4.50_201801-01 |4. ledna 2018 |POST 4.52. |Bude doplněno |
|~~WA-GUEST-OS-4.49_201712-01~~ |4. ledna 2018 |12. březen 2018 |Bude doplněno |
|~~WA-GUEST-OS-4.48_201711-01~~ |14. prosince 2017 |4. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-4.47_201710-02~~ |8 listopadu 2017 |4. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-4.46_201709-01~~ |6 říjen 2017 |14. prosince 2017 |Bude doplněno |
|~~WA-GUEST-OS-4.45_201708-01~~ |24 srpen 2017 |14. prosince 2017 |Bude doplněno |
|~~WA-GUEST-OS-4.44_201707-01~~ |3. srpna 2017 |8 listopadu 2017 |Bude doplněno |
|~~WA-GUEST-OS-4.43_201706-01~~ |7. července 2017 |6 říjen 2017 |Bude doplněno |
|~~WA-GUEST-OS-4.42_201705-01~~ |5 června 2017 |24 srpen 2017 |Bude doplněno |
|~~WA-GUEST-OS-4.41_201704-01~~ |9 může 2017 |3. srpna 2017 |Bude doplněno |
|~~WA-GUEST-OS-4.40_201703-01~~ |10. dubna 2017 |7. července 2017 |Bude doplněno |
|~~WA-GUEST-OS-4.39_201612-01~~ |10. ledna 2017 |5 června 2017 |Bude doplněno |

## <a name="family-3-releases"></a>Uvolní rodiny 3
**Windows Server 2012**

Nainstalované rozhraní .NET framework: 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Data s * se mohou změnit
>
>

| Konfigurační řetězec | Datum vydání | Zakázat datum | Vypršela platnost datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-3.58_201802-01 |19. března 2018 |POST 3.60 |Bude doplněno |
| WA-GUEST-OS-3.57_201801-01 |29. ledna 2018 |POST 3.59 |Bude doplněno |
|~~WA-GUEST-OS-3.56_201712-01~~ |4. ledna 2018 |19. března 2018 |Bude doplněno |
|~~WA-GUEST-OS-3.55_201711-01~~ |14. prosince 2017 |29. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-3.54_201710-02~~ |8 listopadu 2017 |4. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-3.53_201709-01~~ |6 říjen 2017 |14. prosince 2017 |Bude doplněno |
|~~WA-GUEST-OS-3.52_201708-01~~ |24 srpen 2017 |14. prosince 2017 |Bude doplněno |
|~~WA-GUEST-OS-3.51_201707-01~~ |3. srpna 2017 |8 listopadu 2017 |Bude doplněno |
|~~WA-GUEST-OS-3.50_201706-01~~ |7. července 2017 |6 říjen 2017 |Bude doplněno |
|~~WA-GUEST-OS-3.49_201705-01~~ |5 června 2017 |24 srpen 2017 |Bude doplněno |
|~~WA-GUEST-OS-3.48_201704-01~~ |9 může 2017 |3. srpna 2017 |Bude doplněno |
|~~WA-GUEST-OS-3.47_201703-01~~ |10. dubna 2017 |7. července 2017 |Bude doplněno |
|~~WA-GUEST-OS-3.46_201612-01~~ |10. ledna 2017 |5 června 2017 |Bude doplněno |

## <a name="family-2-releases"></a>Uvolní řady 2
**Windows Server 2008 R2 SP1**

Nainstalované rozhraní .NET framework: 3.5, 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Data s * se mohou změnit
>
>

| Konfigurační řetězec | Datum vydání | Zakázat datum | Vypršela platnost datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-2.71_201802-01 |12. březen 2018 |POST 2,73 |Bude doplněno |
| WA-GUEST-OS-2.70_201801-01 |29. ledna 2018 |POST 2.72 |Bude doplněno |
|~~WA-GUEST-OS-2.69_201712-01~~ |4. ledna 2018 |12. březen 2018 |Bude doplněno |
|~~WA-GUEST-OS-2.68_201711-01~~ |14. prosince 2017 |29. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-2.67_201710-02~~ |8 listopadu 2017 |4. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-2.66_201709-01~~ |6 říjen 2017 |14. prosince 2017 |Bude doplněno |
|~~WA-GUEST-OS-2.65_201708-01~~ |24 srpen 2017 |14. prosince 2017 |Bude doplněno |
|~~WA-GUEST-OS-2.64_201707-01~~ |3. srpna 2017 |8 listopadu 2017 |Bude doplněno |
|~~WA-GUEST-OS-2.63_201706-01~~ |7. července 2017 |6 říjen 2017 |Bude doplněno |
|~~WA-GUEST-OS-2.62_201705-01~~ |5 června 2017 |24 srpen 2017 |Bude doplněno |
|~~WA-GUEST-OS-2.61_201704-01~~ |9 může 2017 |3. srpna 2017 |Bude doplněno |
|~~WA-GUEST-OS-2.60_201703-01~~ |10. dubna 2017 |7. července 2017 |Bude doplněno |
|~~WA-GUEST-OS-2.59_201701-01~~ |10. ledna 2017 |5 června 2017 |Bude doplněno |
|~~WA-GUEST-OS-2.58_201612-01~~ |10. ledna 2017 |9 může 2017|Bude doplněno |


## <a name="msrc-patch-updates"></a>Střediska MSRC oprava aktualizací
Seznam oprav, které jsou součástí jednotlivých měsíční verze hostovaného operačního systému je k dispozici [sem][patches].

## <a name="sdk-support"></a>Podpora v sadě SDK
I když [vyřazení zásad pro sadu SDK Azure] [ retire policy sdk] označuje, že verze výše 2.2 jsou podporované, konkrétní pouze hostovaného operačního systému rodiny můžete používat starší verze. Vždy byste měli použít nejnovější podporovanou SDK.

| Skupina hostovaných operačních systémů | Verze kompatibilní sady SDK |
| --- | --- |
| 5 |Verze 2.9.5.1+ |
| 4 |Verze 2.1 + |
| 3 |Verze 1.8 + |
| 2 |Verze 1.3 + |
| 1 |Verze 1.0 + |

## <a name="guest-os-release-information"></a>Informace o verzi operačního systému hosta
Existují tři kalendářních dat, které jsou důležité pro verze hostovaného operačního systému: **verze** datum, **zakázáno** datum, a **vypršení platnosti** datum. Hostovaného operačního systému se považuje za k dispozici, pokud je v portálu a lze vybrat jako cíl hostovaného operačního systému. Když se dosáhne hostovaného operačního systému **zakázáno** datum, odebere se z Azure. Ale všechny cloudové služby cílení na tomto hostovaného operačního systému bude stále fungovat normálně.

Okno mezi **zakázáno** datum a **vypršení platnosti** datum poskytuje vyrovnávací paměť k snadno přechod z jednoho hostovaného operačního systému na jednu novější. Pokud používáte *automatické* jako vaše hostovaného operačního systému, vždy budete mít na nejnovější verzi a nemusíte si dělat starosti o vypršení platnosti.

Když **vypršení platnosti** datum úspěšně projde, všechny cloudové služby stále pomocí tohoto hostovaného operačního systému bude zastavena, odstranit nebo došlo k upgradu. Další informace o zásadách vyřazení [sem][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Vysvětlení rodiny verze operačního systému hosta
Rodiny hostovaného operačního systému jsou založeny na vydaná verze systému Microsoft Windows Server. Hostovaného operačního systému je základní operační systém, který Azure Cloud Services běží na. Každý hostovaného operačního systému má rodiny, verzí a vydání číslo.

* **Rodina hostovaného operačního systému**  
  Verze operačního systému Windows Server, založený na hostovaného operačního systému. Například *rodiny 3* je založena na systému Windows Server 2012.
* **Verze operačního systému hosta**  
  Konkrétní do hostovaného operačního systému rodiny bitové kopie a relevantní [Microsoft Security Response Center (MSRC)] [ msrc] opravy, které jsou dostupné na datum vytváří nové verze hostovaného operačního systému. Ne všechny opravy mohou být zahrnuty.

    Čísla začínají hodnotou 0 a zvýší o 1 pokaždé, když se přidá novou sadu aktualizací. Koncové nuly se zobrazují pouze pokud je to důležité. To znamená že verze 2.10 je jiné, mnohem novější verze než verze 2.1.
* **Verze hostovaného operačního systému**  
  Opětovné vydání verze hostovaného operačního systému. O opětovné vydání v případě Microsoft vyhledá problémy při testování; nutnosti změny. Nejnovější verze vždy nahrazuje všechny předchozí verze, veřejné nebo ne. Portál Azure pouze umožní uživatelům vybrat nejnovější verze pro danou verzi. Nasazení na předchozí vydání spuštěna nejsou obvykle platnost upgradovat v závislosti na závažnosti chybě.

V následujícím příkladu 2 je dané rodině, je na verzi 12 a "rel2" je verze.

**Verze hostovaného operačního systému** – 2.12 rel2

**Konfigurační řetězec pro tuto verzi** -WA-GUEST-operačního systému-2.12_201208-02

Řetězec konfigurace pro hostovaného operačního systému má stejné informace součástí, společně s datum zobrazení, které opravy střediska MSRC považovány za tuto verzi. V tomto příkladu byly střediska MSRC opravy až vrácená pro Windows Server 2008 R2 a včetně srpen 2012 rozhodnuto o zařazení. Jsou zahrnuty pouze opravy konkrétně použití této verzi systému Windows Server. Například pokud střediska MSRC oprava se vztahuje na Microsoft Office, nebude zahrnutý vzhledem k tomu, že produkt není součástí základní bitovou kopii systému Windows Server.

## <a name="guest-os-system-update-process"></a>Proces aktualizace systému operačního systému hosta
Tato stránka obsahuje informace o budoucích verzí hostovaného operačního systému. Zákazníci označili, že chcete vědět, kdy dochází verze jejich role cloudové služby se restartuje, pokud jsou nastavená na "Automatické" aktualizace. Verze hostovaného operačního systému obvykle dojít alespoň pět (5) dnů po vydání, k níž dojde k druhému úterý v každém měsíci střediska MSRC aktualizace. Nové verze zahrnují všechny relevantní opravy střediska MSRC pro každou skupinu hostovaného operačního systému.

Microsoft Azure je neustále vydání aktualizace. Hostovaného operačního systému je jenom jedna taková aktualizace v kanálu. Verze může být ovlivněn mnoha faktorech příliš mnoho, aby seznam sem. Kromě toho Azure je spuštěná na oznámena stovky tisíc počítačů. To znamená, že je možné uvést přesné datum a čas, kdy se restartuje vaše role. Pracujeme na plán pro omezení nebo čas restartování počítače.

Pokud je publikovaná v nové verzi hostovaného operačního systému, může trvat dobu plně rozšíří na Azure. Jako služby Aktualizovaní na novou hostovaného operačního systému, budou se restartují, aby byla dodržena aktualizace domény. Nastavte na "Automatické" aktualizace pomocí služby získají verze první. Po aktualizaci zobrazí se nová verze hostovaného operačního systému, které jsou uvedené pro vaši službu na portálu Azure. Opětovná vydání může dojít během této doby. Některé verze může být nasazena v delší a automatického upgradu restartování počítače nelze provádět mnoho týdny po datu oficiálního vydání. Jakmile hostovaného operačního systému k dispozici, pak explicitně můžete tuto verzi z portálu nebo v konfiguračním souboru.

Značnou část cenné informace o restartování a ukazatele na další informace o technické podrobnosti aktualizace hosta a hostitelským operačním systémem, naleznete v příspěvku MSDN post s názvem [Role Instance restartuje kvůli upgrady operačního systému][restarts].

Pokud ručně aktualizovat vaše hostovaného operačního systému, přečtěte si téma [zásady vyřazení hostovaného operačního systému] [ retirepolicy] Další informace.

## <a name="guest-os-supportability-and-retirement-policy"></a>Možnosti podpory hostovaného operačního systému a zásady vyřazení
Možnosti podpory a vyřazení zásady hostovaného operačního systému je vysvětlen [sem][retirepolicy].

[hostovaného operačního systému aktualizace informačního kanálu RSS,]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/en-us/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: http://www.microsoft.com/security/msrc/default.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/en-us/library/security/ms17-010.aspx

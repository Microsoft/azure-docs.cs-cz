---
title: Seznamte se s nejnovější verzí hostovaného operačního systému Azure | Dokumentace Microsoftu
description: Nejnovější verzi zprávy a Kompatibilita sady SDK pro Azure Cloud Services hostovaného operačního systému.
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
ms.date: 7/3/2018
ms.author: raiye
ms.openlocfilehash: 8f9bb69a334858d5d039c76d6c0d0aec3f0cf6f0
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858084"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure verzí hostovaného operačního systému a matice kompatibility sady SDK
Poskytuje že vám aktuální informace o nejnovější verzi operačního systému hosta Azure uvolní pro cloudové služby. Tyto informace pomohou při plánování způsob upgradu, než je zakázáno hostovaného operačního systému. Je-li konfigurovat vaše role používat *automatické* aktualizace hostovaného operačního systému, jak je popsáno v [nastavení aktualizace operačního systému hosta Azure][Azure Guest OS Update Settings], není důležité, abyste si přečetli tuto stránku.

> [!IMPORTANT]
> Tato stránka se vztahuje na webových a pracovních procesů cloudových služeb rolí, které běží nad hostovaného operačního systému. Provádí **se nedá použít** pro virtuální počítače IaaS.
>
>


> [!TIP]
>  Přihlaste se k odběru [Informační kanál RSS aktualizací operačního systému hosta] nejnovější oznámení o všech změnách hostovaného operačního systému.
>
>

> [!IMPORTANT]
> Od tohoto dne uvedení, pouze nejnovější 2 verze hostovaného operačního systému bude podporované a dostupný na webu Azure Portal.
>
>

Informace o tom, jak aktualizovat vaše hostovaného operačního systému nejste si jistí? Zkontrolujte [to] [ cloud updates] navýšení kapacity.

## <a name="news-updates"></a>Aktualizace zprávy

###### <a name="july-3-2018"></a>**3. července 2018**
Hostovaný operační systém dne vydala.

###### <a name="june-1-2018"></a>**1. června 2018**
Operačního systému hosta mohou vydala.

###### <a name="may-4-2018"></a>**4. května 2018**
Hostovaný operační systém. dubna vydala.

###### <a name="april-6-2018"></a>**6. dubna 2018**
Hostovaný operační systém. března vydala.

###### <a name="march-19-2018"></a>**19. března 2018**
Hostovaný operační systém. února vydala.

###### <a name="january-29-2018"></a>**29. ledna 2018**
Vydala ledna hostovaného operačního systému pro operační systém řady 2 (WA-hosta – operačního systému – 2.70_201801-01) & 3 (WA-hosta – operačního systému – 3.57_201801-01)

###### <a name="january-4-2018"></a>**4. ledna 2018**
Vydala hostovaného operačního systému od operačního systému řady 4 (WA – hosta – operačního systému – 4.50_201801-01) & 5 (WA-hosta – operačního systému – 5.15_201801-01) a obsahuje důležité opravy.  

###### <a name="january-4-2018"></a>**4. ledna 2018**
Vydal se dne hostovaného operačního systému.

###### <a name="december-14-2017"></a>**14. prosince 2017**
Vydal se dne hostovaného operačního systému.

###### <a name="november-8-2017"></a>**8. listopadu 2017**
Říjen hostovaného operačního systému, který vydala.



## <a name="releases"></a>Vydané verze
## <a name="family-5-releases"></a>Uvolní řady 5
**Windows Server 2016**

Nainstalované rozhraní .NET framework: 4.0, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1 a 4.6.2

> [!NOTE]
> Data s * se může změnit.
>
> Hesla protokolu RDP pro operační systém řady 5 musí být minimálně 10 znaků.
>

| Konfigurační řetězec | Datum vydání | Zakázat datum | Neplatné datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-5.20_201806-01 |3. července 2018 |Příspěvek 5.22 |Bude doplněno |
| WA-GUEST-OS-5.19_201805-01 |1. června 2018 |Příspěvek 5.21 |Bude doplněno |
|~~WA-GUEST-OS-5.18_201804-01~~ |4. května 2018 |3. července 2018 |Bude doplněno |
|~~WA-GUEST-OS-5.17_201803-01~~ |6. dubna 2018 |1. června 2018|Bude doplněno |
|~~WA-GUEST-OS-5.16_201802-01~~ |12. března 2018 |4. května 2018 |Bude doplněno |
|~~WA-GUEST-OS-5.15_201801-01~~ |4. ledna 2018 |6. dubna 2018 |Bude doplněno |
|~~WA-GUEST-OS-5.14_201712-01~~ |4. ledna 2018 |12. března 2018 |Bude doplněno |
|~~WA-GUEST-OS-5.13_201711-01~~ |14. prosince 2017 |4. ledna 2018|Bude doplněno |
|~~WA-GUEST-OS-5.12_201710-02~~ |8. listopadu 2017 |4. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-5.11_201709-01~~ |6. října 2017 |14. prosince 2017 |Bude doplněno |


## <a name="family-4-releases"></a>Uvolní řady 4
**Windows Server 2012 R2**

Nainstalované rozhraní .NET framework: 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Data s * se může změnit
>
>

| Konfigurační řetězec | Datum vydání | Zakázat datum | Neplatné datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-4.55_201806-01 |3. července 2018 |Příspěvek 4.57 |Bude doplněno |
| WA-GUEST-OS-4.54_201805-01 |1. června 2018 |Příspěvek 4,56 |Bude doplněno |
|~~WA-GUEST-OS-4.53_201804-01~~ |4. května 2018 |3. července 2018 |Bude doplněno |
|~~WA-GUEST-OS-4.52_201803-01~~ |6. dubna 2018 |1. června 2018 |Bude doplněno |
|~~WA-GUEST-OS-4.51_201802-01~~ |12. března 2018 |4. května 2018 |Bude doplněno |
|~~WA-GUEST-OS-4.50_201801-01~~ |4. ledna 2018 |6. dubna 2018 |Bude doplněno |
|~~WA-GUEST-OS-4.49_201712-01~~ |4. ledna 2018 |12. března 2018 |Bude doplněno |
|~~WA-GUEST-OS-4.48_201711-01~~ |14. prosince 2017 |4. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-4.47_201710-02~~ |8. listopadu 2017 |4. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-4.46_201709-01~~ |6. října 2017 |14. prosince 2017 |Bude doplněno |



## <a name="family-3-releases"></a>Uvolní řady 3
**Windows Server 2012**

Nainstalované rozhraní .NET framework: 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Data s * se může změnit
>
>

| Konfigurační řetězec | Datum vydání | Zakázat datum | Neplatné datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-3.62_201806-01 |3. července 2018 |Příspěvek 3.64 |Bude doplněno |
| WA-GUEST-OS-3.61_201805-01 |1. června 2018 |Příspěvek 3.63 |Bude doplněno |
|~~WA-GUEST-OS-3.60_201804-01~~ |4. května 2018 |3. července 2018 |Bude doplněno |
|~~WA-GUEST-OS-3.59_201803-01~~ |6. dubna 2018 |1. června 2018 |Bude doplněno |
|~~WA-GUEST-OS-3.58_201802-01~~ |19. března 2018 |4. května 2018 |Bude doplněno |
|~~WA-GUEST-OS-3.57_201801-01~~ |29. ledna 2018 |6. dubna 2018 |Bude doplněno |
|~~WA-GUEST-OS-3.56_201712-01~~ |4. ledna 2018 |19. března 2018 |Bude doplněno |
|~~WA-GUEST-OS-3.55_201711-01~~ |14. prosince 2017 |29. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-3.54_201710-02~~ |8. listopadu 2017 |4. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-3.53_201709-01~~ |6. října 2017 |14. prosince 2017 |Bude doplněno |
|~~WA-GUEST-OS-3.52_201708-01~~ |24. srpna 2017 |14. prosince 2017 |Bude doplněno |


## <a name="family-2-releases"></a>Uvolní řady 2
**Windows Server 2008 R2 SP1**

Nainstalované rozhraní .NET framework: 3.5, 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Data s * se může změnit
>
>

| Konfigurační řetězec | Datum vydání | Zakázat datum | Neplatné datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-2.75_201806-01 |3. července 2018 |Příspěvek 2.77 |Bude doplněno |
| WA-GUEST-OS-2.74_201805-01 |1. června 2018 |Příspěvek 2.76 |Bude doplněno |
|~~WA-GUEST-OS-2.73_201804-01~~ |4. května 2018 |3. července 2018 |Bude doplněno |
|~~WA-GUEST-OS-2.72_201803-01~~ |6. dubna 2018 |1. června 2018 |Bude doplněno |
|~~WA-GUEST-OS-2.71_201802-01~~ |12. března 2018 |4. května 2018 |Bude doplněno |
|~~WA-GUEST-OS-2.70_201801-01~~ |29. ledna 2018 |6. dubna 2018 |Bude doplněno |
|~~WA-GUEST-OS-2.69_201712-01~~ |4. ledna 2018 |12. března 2018 |Bude doplněno |
|~~WA-GUEST-OS-2.68_201711-01~~ |14. prosince 2017 |29. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-2.67_201710-02~~ |8. listopadu 2017 |4. ledna 2018 |Bude doplněno |
|~~WA-GUEST-OS-2.66_201709-01~~ |6. října 2017 |14. prosince 2017 |Bude doplněno |
|~~WA-GUEST-OS-2.65_201708-01~~ |24. srpna 2017 |14. prosince 2017 |Bude doplněno |


## <a name="msrc-patch-updates"></a>Aktualizace MSRC
Seznam oprav, které jsou součástí jednotlivých vydání měsíční aktualizace operačního systému hosta je k dispozici [tady][patches].

## <a name="sdk-support"></a>Podpora v sadě SDK
I v případě, [zásady vyřazení z provozu pro sadu Azure SDK] [ retire policy sdk] označuje, že pouze verze nad 2.2 jsou podporované, konkrétní řady hostovaného operačního systému vám umožní použít starší verze. Vždy používejte nejnovější podporované SDK.

| Řada operačního systému hosta | Verze kompatibilní sady SDK |
| --- | --- |
| 5 |Verze 2.9.5.1+ |
| 4 |Verze 2.1 + |
| 3 |Verze 1.8 a |
| 2 |Verze 1.3 + |
| 1 |Verze 1.0 a |

## <a name="guest-os-release-information"></a>Informace o verzi operačního systému hosta
Existují tři kalendářních dat, které jsou důležité pro verzí hostovaného operačního systému: **release** datum, **zakázané** data, a **vypršení platnosti** datum. Hostovaný operační systém se považuje za k dispozici, pokud je na portálu a lze vybrat jako cíl hostovaného operačního systému. Když se dosáhne hostovaného operačního systému **zakázané** datum, odebere se z Azure. Ale kteroukoli Cloudovou službu, která cílí na tuto hostovaného operačního systému bude pak stále fungovat jako obvykle.

V okně mezi **zakázané** datum a **vypršení platnosti** datum vám poskytne vyrovnávací paměť snadno přejít z jednoho hostovaného operačního systému do jednoho novější. Pokud používáte *automatické* jako vaše hostovaného operačního systému, vždy budete mít nejnovější verzi a nemusíte se starat o jeho platnost vyprší.

Když **vypršení platnosti** předá data, kteroukoli Cloudovou službu pořád používá tento hostovaného operačního systému se mohly zastavit, odstranit nebo muset upgradovat. Další informace o zásadách vyřazení [tady][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Vysvětlení řady verze operačního systému hosta
Řady hostovaného operačního systému jsou založeny na vydané verze systému Microsoft Windows Server. Hostovaný operační systém je základní operační systém spuštěný v Azure Cloud Services. Každý hostovaný operační systém má rodiny, verzi a vydání číslo.

* **Řada operačního systému hosta**  
  Verze operačního systému Windows Server, založený na operačním systému hosta. Například *řady 3* je založená na Windows serveru 2012.
* **Verze operačního systému hosta**  
  Specifické pro rodinu bitovou kopii operačního systému hosta a relevantní [Microsoft Security Response Center (MSRC)] [ msrc] opravy, které jsou dostupné na datum vygenerované nová verze hostovaného operačního systému. Ne všechny opravy mohou být zahrnuty.

    Čísla začínají hodnotou 0 a přírůstek na 1 pokaždé, když se přidá novou sadu aktualizací. Koncové nuly jsou uvedeny pouze pokud je to důležité. To znamená verze 2.10 je jiný, mnohem vyšší verze než verze 2.1.
* **Vydání hostovaného operačního systému**  
  Opětovné vydání verze hostovaného operačního systému. O opětovné vydání nastane, pokud Microsoft zjistí problémy během testování. vyžaduje změny. Nejnovější vydaná verze vždy nahrazuje jakékoli předchozí verze, veřejného nebo ne. Na webu Azure portal bude pouze uživatelům povolit vybrat nejnovější verzi pro danou verzi. Nasazení, které běží na předchozí verzi nejsou obvykle platnost upgradovat v závislosti na závažnosti chyby.

V následujícím příkladu 2 je rodině, 12 je verze a "rel2" je verze.

**Vydání hostovaného operačního systému** – 2.12 rel2

**Konfigurační řetězec pro tuto verzi** -WA-GUEST-operačního systému – 2.12_201208-02

Konfigurační řetězec pro hostovaný operační systém obsahuje tytéž informace součástí, spolu s data zobrazení, které opravy MSRC byla zvážena pro tuto verzi. V tomto příkladu byla zvážena MSRC opravy vytvořil pro Windows Server 2008 R2 až a včetně srpen 2012 pro zahrnutí. Jsou zahrnuty pouze opravy konkrétně použití pro tuto verzi Windows serveru. Například pokud oprava MSRC platí pro Microsoft Office, nebude zahrnutý protože tento produkt není součástí základní image Windows serveru.

## <a name="guest-os-system-update-process"></a>Proces aktualizace systému operačního systému hosta
Tato stránka obsahuje informace o nadcházejících verzí hostovaného operačního systému. Zákazníci označili, že chtějí vědět, kdy vydané verzi dochází, protože jejich rolí cloudové služby se restartuje, pokud jsou nastavená na "Automatické" aktualizace. Verzí hostovaného operačního systému obvykle dochází minimálně pět (5) dní po MSRC aktualizovat verzi, ke které dojde k druhému úterý v každém měsíci. Nová verze zahrnují všechny příslušné opravy MSRC pro každou řadu hostovaného operačního systému.

Microsoft Azure je neustále uvolnění aktualizace. Hostovaný operační systém je pouze jeden tyto aktualizace v kanálu. Verze může mít vliv celá řada faktorů, příliš mnoho, aby seznam tady. Kromě toho Azure běží v doslova stovky tisíc počítačů. To znamená, že je možné uvést přesné datum a čas, kdy se restartuje vaše role. Pracujeme na plán na omezení nebo čas restartování počítače.

Když se publikuje novou verzi hostovaného operačního systému, může trvat dobu plně rozšíří do Azure. Protože služby jsou aktualizované na novou hostovaného operačního systému, se restartují dodržením aktualizační domény. Služby nastaveno pro použití aktualizací "Automatické" se zobrazí první vydání verze. Po aktualizaci se zobrazí nová verze hostovaného operačního systému, které jsou uvedené pro vaši službu na portálu Azure portal. Během této doby může dojít k opakovaným vydáním. Některé verze mohou být nasazeny delší časová období a automatického upgradu restartování počítače nelze provádět mnoho týdny po datu oficiálním vydáním. Jakmile hostovaného operačního systému je k dispozici, pak explicitně můžete tuto verzi z portálu nebo v konfiguračním souboru.

Značnou cenné informace o restartování a odkazy na další informace o technické podrobnosti aktualizace hosta a hostitelským operačním systémem, najdete v článku MSDN blogový příspěvek s názvem [Role Instance se restartuje kvůli upgrady operačního systému] [ restarts].

Pokud ručně aktualizovat vaše hostovaného operačního systému, najdete v článku [zásady vyřazení z provozu pro hostovaný operační systém] [ retirepolicy] pro další informace.

## <a name="guest-os-supportability-and-retirement-policy"></a>Možnosti podpory hostovaného operačního systému a zásady vyřazení z provozu
Vysvětlení zásad podpoře a vyřazení hostovaného operačního systému [tady][retirepolicy].

[cloud updates]: https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-update-azure-service
[Informační kanál RSS aktualizací operačního systému hosta]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
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
[msrc]: https://technet.microsoft.com/en-us/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx

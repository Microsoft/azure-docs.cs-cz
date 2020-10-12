---
title: Přečtěte si o nejnovějších verzích hostovaných operačních systémů Azure | Microsoft Docs
description: Nejnovější novinky k vydání a kompatibilita SDK pro Azure Cloud Services hostovaný operační systém.
services: cloud-services
documentationcenter: na
author: yohaddad
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 8/17/2020
ms.author: yohaddad
ms.openlocfilehash: d79fd60f4fe3786cfe3106329904d9d262078411
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90024209"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Verze hostovaného operačního systému Azure a tabulka kompatibility SDK
Poskytuje aktuální informace o nejnovějších vydáních hostovaného operačního systému Azure pro Cloud Services. Tyto informace vám pomohou naplánovat cestu upgradu před zakázáním hostovaného operačního systému. Pokud vaše role nakonfigurujete tak, aby používaly *Automatické* aktualizace HOSTOVANÉHO operačního systému, jak je popsáno v tématu [aktualizace nastavení operačního systému hosta v Azure][Azure Guest OS Update Settings], není důležité tuto stránku číst.

> [!IMPORTANT]
> Tato stránka se vztahuje na Cloud Services webové a pracovní role, které se spouštějí nad hostovaným operačním systémem. **Nevztahuje se na IaaS** Virtual Machines.
>
>


> [!TIP]
>  Přihlaste se k odběru [informačního kanálu RSS aktualizace hostovaného operačního systému] , abyste získali nejaktuálnější oznámení o všech změnách operačního systému hosta.
>
>

> [!IMPORTANT]
> V Azure Portal bude podporována a k dispozici pouze nejnovější 2 verze hostovaného operačního systému.
>
>

Nevíte, jak aktualizovat hostovaný operační systém? Podívejte se na [Toto][cloud updates] .

## <a name="news-updates"></a>Aktualizace zpráv

###### <a name="september-5-2020"></a>**5. září 2020**
Hostovaný operační systém od srpna vydala. 

###### <a name="august-17-2020"></a>**17. srpna 2020**
Hostovaný operační systém v červenci byl uvolněn. 

###### <a name="august-10-2020"></a>**10. srpna 2020**
Hostovaný operační systém v červnu vydal. 

###### <a name="june-2-2020"></a>**2. června 2020**
Hostovaný operační systém může být uvolněn. 

###### <a name="may-4-2020"></a>**4. května 2020**
Hostovaný operační systém v dubnu byl uvolněn. 

###### <a name="april-2-2020"></a>**2. dubna 2020**
Hostovaný operační systém v březnu byl uvolněn. 

###### <a name="march-5-2020"></a>**5. března 2020**
Hostovaný operační systém v únoru vydal. 

###### <a name="january-24-2020"></a>**24. ledna 2020**
Hostovaný operační systém v lednu vydal. 

###### <a name="january-8-2020"></a>**8. ledna 2020**
Hostovaný operační systém v prosinci byl vydán.

###### <a name="december-5-2019"></a>**5. prosince 2019**
Operační systém hosta v listopadu vydal.

###### <a name="november-1-2019"></a>**1. listopadu 2019**
Hostovaný operační systém v říjnu vydal.

###### <a name="october-7-2019"></a>**7. října 2019**
Hostovaný operační systém v září vydal.

###### <a name="september-4-2019"></a>**4. září 2019**
Hostovaný operační systém od srpna vydala.

###### <a name="july-26-2019"></a>**26. července 2019**
Hostovaný operační systém v červenci byl uvolněn.

###### <a name="july-8-2019"></a>**8. července 2019**
Hostovaný operační systém v červnu vydal.

###### <a name="june-6-2019"></a>**6. června 2019**
Hostovaný operační systém může být uvolněn.

###### <a name="may-7-2019"></a>**7. května 2019**
Hostovaný operační systém v dubnu byl uvolněn.

###### <a name="march-26-2019"></a>**26. března 2019**
Hostovaný operační systém v březnu byl uvolněn.

###### <a name="march-12-2019"></a>**12. března 2019**
Hostovaný operační systém v únoru vydal.

###### <a name="february-5-2019"></a>**5. února 2019**
Hostovaný operační systém v lednu vydal.

###### <a name="january-24-2019"></a>**24. ledna 2019**
Vydání hostovaného operačního systému řady 6 (Windows Server 2019).

###### <a name="january-7-2019"></a>**7. ledna 2019**
Hostovaný operační systém v prosinci byl vydán.

###### <a name="december-14-2018"></a>**14. prosince 2018**
Operační systém hosta v listopadu vydal.

###### <a name="november-8-2018"></a>**8. listopadu 2018**
Hostovaný operační systém v říjnu vydal.

###### <a name="october-12-2018"></a>**12. října 2018**
Hostovaný operační systém v září vydal.

## <a name="releases"></a>Verze

## <a name="family-6-releases"></a>Vydání řady 6
**Windows Server 2019**

.NET Framework nainstalován: 3,5, 4.7.2

> [!NOTE]
> Sadu Windows Azure SDK for .NET-3,0 si můžete stáhnout [tady][Windows Azure SDK].
>
>Kroky instalace:
>1. Odinstalujte prosím všechny starší verze MicrosoftAzureAuthoringTools*. msi.
>2. Instalace [sady Azure SDK pro .NET – 3,0][Windows Azure SDK]
>3. Restartujte počítač.
>4. Vytvořte nový projekt cloudové služby a přidejte jednu roli pracovního procesu.
>5. Změna řady OS na 6 a sestavení balíčku
>6. Nasazení balíčku do Azure pomocí Azure Portal nebo sady Visual Studio
>
>Vydání verze s operačním systémem Host 6 vynutila TLS 1,2 tím, že explicitně zakáže TLS 1,0 a 1,1 a definuje konkrétní sadu šifrovacích sad. [Další]informace


| Konfigurační řetězec | Datum vydání | Zakázat datum |
| --- | --- | --- |
|  WA-HOST-OS-6.22 _202008-02  |  5. září 2020  |  Post 6,24  |  
|  WA-HOST-OS-6.21 _202007-01  |  17. srpna 2020  |  Post 6,23  |  
|~~WA-HOST-OS-6.20 _202006-02~~|  10. srpna 2020  |  5. září 2020  |  
|~~WA-HOST-OS-6.19 _202005-02~~|  2. června 2020  |  17. srpna 2020  |  
|~~WA-HOST-OS-6.18 _202004-01~~|  4. května 2020  |  10. srpna 2020  |  
|~~WA-HOST-OS-6.17 _202003-01~~|  2. dubna 2020  |  2. června 2020  |  
|~~WA-HOST-OS-6.16 _202002-01~~|  5. března 2020  |  4. května 2020  |  
|~~WA-HOST-OS-6.15 _202001-01~~|  24. ledna 2020  |  2. dubna 2020  |  
|~~WA-HOST-OS-6.14 _201912-01~~| 8. ledna 2020 | 5. března 2020 |  
|~~WA-HOST-OS-6.13 _201911-01~~| 5. prosince 2019 | 24. ledna 2020 |  
|~~WA-HOST-OS-6,12 _201910-01~~| 1. listopadu 2019 | 8. ledna 2020 |  
|~~WA-HOST-OS-6.11 _201909-01~~| 7. října 2019 | 5. prosince 2019 |  
|~~WA-HOST-OS-6.10 _201908-01~~| 4. srpna 2019 | 1. listopadu 2019  |  
|~~WA-HOST-OS-_201907-0~~|26. července 2019 | 7. října 2019 |
|~~WA-HOST-OS-6.8 _201906-01~~|8. července 2019 |4. srpna 2019 |
|~~WA-HOST-OS-6.7 _201905-01~~ |6. června 2019 |26. července 2019 |
|~~WA-HOST-OS-6.6 _201904-01~~ |7. května 2019 |8. července 2019 |
|~~WA-HOST-OS-6.5 _201903-01~~ |26. března 2019 |6. června 2019 |
|~~WA-HOST-OS-6.4 _201902-01~~ |12. března 2019 |7. května 2019 |
|~~WA-HOST-OS-6.3 _201901-01~~ |5. února 2019 |26. března 2019 |
|~~WA-HOST-OS-6.2 _201812-01~~ |24. ledna 2019 |12. března 2019 |
|~~WA-HOST-OS-6.1 _201811-01~~ |24. ledna 2019 |5. února 2019 |

## <a name="family-5-releases"></a>Vydání řady 5
**Windows Server 2016**

.NET Framework nainstalován: 3,5, 4.6.2

> [!NOTE]
> Heslo protokolu RDP pro řadu OS 5 musí být minimálně 10 znaků.
>


| Konfigurační řetězec | Datum vydání | Zakázat datum |
| --- | --- | --- |
|  WA-HOST-OS-5.46 _202008-02  |  5. září 2020  |  Post 5,48  |  
|  WA-HOST-OS-5.45 _202007-01  |  17. srpna 2020  |  Post 5,47  |  
|~~WA-HOST-OS-5.44 _202006-02~~|  10. srpna 2020  |  5. září 2020  |  
|~~WA-HOST-OS-5.43 _202005-02~~|  2. června 2020  |  17. srpna 2020  |  
|~~WA-HOST-OS-5.42 _202004-01~~|  4. května 2020  |  10. srpna 2020  |  
|~~WA-HOST-OS-5.41 _202003-01~~|  2. dubna 2020  |  2. června 2020  |  
|~~WA-HOST-OS-5.40 _202002-01~~|  5. března 2020  |  4. května 2020  |  
|~~WA-HOST-OS-5.39 _202001-01~~|  24. ledna 2020  |  2. dubna 2020  |  
|~~WA-HOST-OS-5.38 _201912-01~~| 8. ledna 2020 | 5. března 2020 |  
|~~WA-HOST-OS-5.37 _201911-01~~| 5. prosince 2019 | 24. ledna 2020 |  
|~~WA-HOST-OS-5.36 _201910-01~~| 1. listopadu 2019 | 8. ledna 2020 |  
|~~WA-HOST-OS-5.35 _201909-01~~| 7. října 2019 | 5. prosince 2019 |  
|~~WA-HOST-OS-5.34 _201908-01~~|  4. srpna 2019  | 1. listopadu 2019 |  
|~~WA-HOST-OS-5.33 _201907-01~~| 26. července 2019 | 7. října 2019 |  
|~~WA-HOST-OS-5.32 _201906-01~~|8. července 2019 |4. srpna 2019 |
|~~WA-HOST-OS-5.31 _201905-01~~ |6. června 2019 |26. července 2019 |
|~~WA-HOST-OS-5.30 _201904-01~~ |7. května 2019 |8. července 2019 |
|~~WA-HOST-OS-5.29 _201903-01~~ |26. března 2019 |6. června 2019 |
|~~WA-HOST-OS-5.28 _201902-01~~ |12. března 2019 |7. května 2019 |
|~~WA-HOST-OS-5.27 _201901-01~~ |5. února 2019 |26. března 2019 |
|~~WA-HOST-OS-5.26 _201812-01~~ |7. ledna 2019 |12. března 2019 |
|~~WA-HOST-OS-5,25 _201811-01~~ |14. prosince 2018 |5. února 2019 |
|~~WA-HOST-OS-5.24 _201810-01~~ |8. listopadu 2018 |7. ledna 2019 |
|~~WA-HOST-OS-5.23 _201809-01~~ |12. října 2018 |14. prosince 2018 |

## <a name="family-4-releases"></a>Verze 4 pro Family
**Windows Server 2012 R2**

.NET Framework nainstalován: 3,5, 4.5.1, 4.5.2

| Konfigurační řetězec | Datum vydání | Zakázat datum |
| --- | --- | --- |
|  WA-HOST-OS-4.81 _202008-02  |  5. září 2020  |  Post 4,83  | 
|  WA-HOST-OS-4.80 _202007-01  |  17. srpna 2020  |  Post 4,82  | 
|~~WA-HOST-OS-4.79 _202006-02~~|  10. srpna 2020  |  5. září 2020  | 
|~~WA-HOST-OS-4.78 _202005-02~~|  2. června 2020  |  17. srpna 2020  |  
|~~WA-HOST-OS-4.77 _202004-01~~|  4. května 2020  |  10. srpna 2020  |  
|~~WA-HOST-OS-4.76 _202003-01~~|  2. dubna 2020  |  2. června 2020  |  
|~~WA-HOST-OS-4.75 _202002-01~~|  5. března 2020  |  4. května 2020  |  
|~~WA-HOST-OS-4.74 _202001-01~~|  24. ledna 2020  |  2. dubna 2020  |  
|~~WA-HOST-OS-4.73 _201912-01~~| 8. ledna 2020 | 5. března 2020 |  
|~~WA-HOST-OS-4.72 _201911-01~~| 5. prosince 2019 | 24. ledna 2020 |  
|~~WA-HOST-OS-4.71 _201910-01~~| 1. listopadu 2019 | 8. ledna 2020 |  
|~~WA-HOST-OS-4.70 _201909-01~~| 7. října 2019 | 5. prosince 2019 |  
|~~WA-HOST-OS-4.69 _201908-01~~| 4. srpna 2019 | 1. listopadu 2019 |  
|~~WA-HOST-OS-4.68 _201907-01~~| 26. července 2019  | 7. října 2019 |
|~~WA-HOST-OS-4.67 _201906-01~~| 8. července 2019 |4. srpna 2019 |
|~~WA-HOST-OS-4.66 _201905-01~~ |6. června 2019 |26. července 2019 |
|~~WA-HOST-OS-4.65 _201904-01~~ |7. května 2019 |8. července 2019 |
|~~WA-HOST-OS-4.64 _201903-01~~ |26. března 2019 |6. června 2019 |
|~~WA-HOST-OS-4.63 _201902-01~~ |12. března 2019 |7. května 2019 |
|~~WA-HOST-OS-4.62 _201901-01~~ |5. února 2019 |26. března 2019 |
|~~WA-HOST-OS-4.61 _201812-01~~ |7. ledna 2019 |12. března 2019 |
|~~WA-HOST-OS-4.60 _201811-01~~ |14. prosince 2018 |5. února 2019 |
|~~WA-HOST-OS-4.59 _201810-01~~ |8. listopadu 2018 |7. ledna 2019 |
|~~WA-HOST-OS-4.58 _201809-01~~ |12. října 2018 |14. prosince 2018 |

## <a name="family-3-releases"></a>Verze Family 3
**Windows Server 2012**

.NET Framework nainstalován: 3,5, 4,5

| Konfigurační řetězec | Datum vydání | Zakázat datum |
| --- | --- | --- |
|  WA-HOST-OS-3.88 _202008-02  |  5. září 2020  |  Post 3,90  |  
|  WA-HOST-OS-3.87 _202007-01  |  17. srpna 2020  |  Post 3,89  |  
|~~WA-HOST-OS-3.86 _202006-02~~|  10. srpna 2020  |  5. září 2020  |  
|~~WA-HOST-OS-3.85 _202005-02~~|  2. června 2020  |  17. srpna 2020  |  
|~~WA-HOST-OS-3.84 _202004-01~~|  4. května 2020  |  10. srpna 2020  |  
|~~WA-HOST-OS-3.83 _202003-01~~|  2. dubna 2020  |  2. června 2020  |  
|~~WA-HOST-OS-3.82 _202002-01~~|  5. března 2020  |  4. května 2020  |  
|~~WA-HOST-OS-3.81 _202001-01~~|  24. ledna 2020  |  2. dubna 2020  |  
|~~WA-HOST-OS-3.80 _201912-01~~| 8. ledna 2020 | 5. března 2020 |  
|~~WA-HOST-OS-3.79 _201911-01~~| 5. prosince 2019 | 24. ledna 2020 |  
|~~WA-HOST-OS-3.78 _201910-01~~| 1. listopadu 2019 | 8. ledna 2020 |  
|~~WA-HOST-OS-3.77 _201909-01~~| 7. října 2019 | 5. prosince 2019 |  
|~~WA-HOST-OS-3.76 _201908-01~~|  4. srpna 2019  |  1. listopadu 2019  |  
|~~WA-HOST-OS-3,75 _201907-01~~| 26. července 2019 | 7. října 2019 |
|~~WA-HOST-OS-3.74 _201906-01~~| 8. července 2019 |4. srpna 2019 |
|~~WA-HOST-OS-3.73 _201905-01~~ |6. června 2019 |26. července 2019 |
|~~WA-HOST-OS-3.72 _201904-01~~ |7. května 2019 |8. července 2019 |
|~~WA-HOST-OS-3.71 _201903-01~~ |26. března 2019 |6. června 2019 |
|~~WA-HOST-OS-3.70 _201902-01~~ |12. března 2019 |7. května 2019 |
|~~WA-HOST-OS-3.69 _201901-01~~ |5. února 2019 |26. března 2019 |
|~~WA-HOST-OS-3.68 _201812-01~~ |7. ledna 2019 |12. března 2019 |
|~~WA-HOST-OS-3.67 _201811-01~~ |14. prosince 2018 |5. února 2019 |
|~~WA-HOST-OS-3.66 _201810-01~~ |8. listopadu 2018 |7. ledna 2019 |
|~~WA-HOST-OS-3.65 _201809-01~~ |12. října 2018 |14. prosince 2018 |

## <a name="family-2-releases"></a>Verze Family 2
**Windows Server 2008 R2 SP1**

.NET Framework nainstalován: 3,5 (zahrnuje 2,0 a 3,0), 4,5

| Konfigurační řetězec | Datum vydání | Zakázat datum |
| --- | --- | --- |
|  WA-HOST-OS-2.101 _202008-02  |  5. září 2020  |  Post 2,103  |  
|  WA-HOST-OS-2.100 _202007-01  |  17. srpna 2020  |  Post 2,102  |  
|~~WA-HOST-OS-2.99 _202006-02~~|  10. srpna 2020  | 5. září 2020  |  
|~~WA-HOST-OS-2.98 _202005-02~~|  2. června 2020  |  17. srpna 2020  |  
|~~WA-HOST-OS-2.97 _202004-01~~|  4. května 2020  |  10. srpna 2020  |  
|~~WA-HOST-OS-2.96 _202003-01~~|  2. dubna 2020  |  2. června 2020  |  
|~~WA-HOST-OS-2.95 _202002-01~~|  5. března 2020  |  4. května 2020  |  
|~~WA-HOST-OS-2.94 _202001-01~~|  24. ledna 2020  |  2. dubna 2020  |  
|~~WA-HOST-OS-2.93 _201912-01~~| 8. ledna 2020 | 5. března 2020 |  
|~~WA-HOST-OS-2.92 _201911-01~~| 5. prosince 2019 | 24. ledna 2020 |  
|~~WA-HOST-OS-2.91 _201910-01~~| 1. listopadu 2019 | 8. ledna 2020 |  
|~~WA-HOST-OS-2.90 _201909-01~~| 7. října 2019 | 5. prosince 2019 |  
|~~WA-HOST-OS-2.89 _201908-01~~| 4. srpna 2019 | 1. listopadu 2019 |  
|~~WA-HOST-OS-2,88 _201907-01~~| 26. července 2019 | 7. října 2019 |
|~~WA-HOST-OS-2.87 _201906-01~~|8. července 2019 | 4. srpna 2019 |
|~~WA-HOST-OS-2.86 _201905-01~~ |6. června 2019 |26. července 2019 |
|~~WA-HOST-OS-2.85 _201904-01~~ |7. května 2019 |8. července 2019 |
|~~WA-HOST-OS-2.84 _201903-01~~ |26. března 2019 |6. června 2019 |
|~~WA-HOST-OS-2.83 _201902-01~~ |12. března 2019 |7. května 2019 |
|~~WA-HOST-OS-2.82 _201901-01~~ |5. února 2019 |26. března 2019 |
|~~WA-HOST-OS-2.81 _201812-01~~ |7. ledna 2019 |12. března 2019 |
|~~WA-HOST-OS-2.80 _201811-01~~ |14. prosince 2018 |5. února 2019 |
|~~WA-HOST-OS-2.79 _201810-01~~ |8. listopadu 2018 |7. ledna 2019 |
|~~WA-HOST-OS-2.78 _201809-01~~ |12. října 2018 |14. prosince 2018 |

## <a name="msrc-patch-updates"></a>Aktualizace oprav MSRC
Seznam oprav, které jsou součástí každé měsíční verze hostovaného operačního systému, je k dispozici [zde][patches].

## <a name="sdk-support"></a>Podpora SDK
I když [zásada vyřazení pro sadu Azure SDK][retire policy sdk] uvádí, že jsou podporované jenom verze 2,2, konkrétní rodiny HOSTOVANÝCH operačních systémů vám umožní používat starší verze. Vždycky byste měli používat nejnovější podporovanou sadu SDK.

| Řada hostovaných operačních systémů | Kompatibilní verze sady SDK |
| --- | --- |
| 6 |Verze 2.9.6 + |
| 5 |Verze 2.9.5.1 + |
| 4 |Verze 2.1 + |
| 3 |Verze 1.8 + |
| 2 |Verze 1.3 + |
| 1 |Verze 1.0 + |

## <a name="guest-os-release-information"></a>Informace k vydání hostovaného operačního systému
Existují tři kalendářní data, která jsou důležitá pro vydání hostovaných operačních systémů: datum **vydání** , datum **zakázání** a datum **vypršení platnosti** . Hostovaný operační systém se považuje za dostupný, když je na portálu a dá se vybrat jako cílový hostovaný operační systém. Když hostovaný operační systém dosáhne data **zakázání** , odebere se z Azure. Nicméně všechny cloudové služby, které cílí na hostovaný operační systém, budou i nadále fungovat jako normálně.

Okno mezi **zakázaným** datem a datem **vypršení platnosti** poskytuje vyrovnávací paměť, která umožňuje snadný přechod z jednoho hostovaného operačního systému na jeden novější. Pokud používáte *Automatické* použití jako hostovaný operační systém, budete mít vždycky na nejnovější verzi a nemusíte si dělat starosti s tím, že vyprší platnost.

Po uplynutí data **vypršení platnosti** budou všechny cloudové služby, které tento hostovaný operační systém stále používají, zastaveny, odstraněny nebo vynuceny upgradovat. [Tady][retirepolicy]si můžete přečíst další informace o zásadách pro vyřazení.

## <a name="guest-os-family-version-explanation"></a>Řada operačních systémů hosta – vysvětlení verze
Řady hostovaných operačních systémů jsou založené na vydaných verzích systému Microsoft Windows Server. Hostovaný operační systém je základní operační systém, na kterém běží Azure Cloud Services. Každý hostovaný operační systém má řadu, verzi a číslo verze.

* **Řada hostovaných operačních systémů**  
  Verze operačního systému Windows Server, na kterém je založen hostovaný operační systém. Například *rodina 3* je založena na systému Windows Server 2012.
* **Verze operačního systému hosta**  
  Specifická pro Image řady hostovaného operačního systému a příslušné opravy [služby Microsoft Security Response Center (MSRC)][msrc] , které jsou k dispozici v datu, kdy se vytvoří nová verze operačního systému hosta. Ne všechny opravy mohou být zahrnuty.

    Čísla začínají na 0 a při každém přidání nové sady aktualizací se zvýší o 1. Koncové nuly jsou zobrazeny pouze v případě, že jsou důležité. To znamená, že verze 2,10 je odlišná, mnohem novější než verze 2,1.
* **Verze operačního systému hosta**  
  Opětovné vydání verze hostovaného operačního systému. K opětovnému vydání dojde v případě, že společnost Microsoft při testování najde problémy; vyžadování změn. Nejnovější verze vždycky nahrazuje všechny předchozí verze, veřejné nebo ne. Azure Portal umožní uživatelům vybrat nejnovější verzi pro danou verzi. Nasazení spuštěná v předchozí vydané verzi většinou nejsou vynuceně upgradována v závislosti na závažnosti chyby.

V následujícím příkladu je 2 Rodina, 12 je verze a "REL2" je verze.

**Vydání hostovaného operačního systému** – 2,12 REL2

**Konfigurační řetězec pro tuto verzi** – WA-host-OS-2.12 _201208-02

Konfigurační řetězec pro hostovaný operační systém obsahuje stejné informace, které jsou v něm vložené, spolu s datem, který ukazuje, které opravy MSRC byly pro danou verzi zváženy. V tomto příkladu se za zahrnutí považují opravy MSRC vydané pro Windows Server 2008 R2 až do a včetně srpna 2012. Součástí jsou jenom opravy, které se konkrétně použijí pro tuto verzi Windows serveru. Pokud se třeba oprava MSRC vztahuje na systém Microsoft Office, nebude zahrnutá, protože tento produkt není součástí základní image Windows serveru.

## <a name="guest-os-system-update-process"></a>Proces aktualizace systému hostovaného operačního systému
Tato stránka obsahuje informace o nadcházejících vydáních hostovaného operačního systému. Zákazníci uvedli, že chtějí znát, kdy k vydaným verzím dochází, protože jejich role cloudové služby se restartují, pokud jsou nastavené na automatické aktualizace. Verze hostovaného operačního systému se obvykle vyskytují 2-3 týdnů po vydání aktualizace MSRC, ke kterému dochází v druhém úterý v měsíci. Nové verze zahrnují všechny relevantní opravy MSRC pro každou rodinu operačních systémů.

Microsoft Azure neustále uvolňuje aktualizace. Hostovaný operační systém je v kanálu jenom jedna aktualizace. Vydaná verze může být ovlivněná mnoha faktory, a to v seznamu je příliš mnoho. Kromě toho Azure běží na doslova stovek tisíc počítačů. To znamená, že není možné poskytnout přesné datum a čas, kdy se vaše role restartují. Pracujeme na tom, jak na základě plánu omezit nebo restartovat.

Když je publikovaná nová verze hostovaného operačního systému, může trvat déle, než se plně rozšíří napříč Azure. Jak jsou služby aktualizovány na nový hostovaný operační systém, restartují se tyto aktualizační domény. Služby nastavené na používání automatických aktualizací obdrží nejdřív vydání. Po aktualizaci se zobrazí nová verze hostovaného operačního systému uvedená pro vaši službu v Azure Portal. K opětovnému vydání může během této doby dojít. Některé verze se můžou nasadit za delší dobu a restartování automatického upgradu se nemusí vyskytnout po celou dobu oficiálního vydání. Jakmile je hostovaný operační systém k dispozici, můžete ho explicitně zvolit z portálu nebo v konfiguračním souboru.

Skvělé informace o restartech a ukazatelích na Další informace technické podrobnosti o aktualizacích operačních systémů host a Host najdete v blogovém příspěvku MSDN s názvem [instance role restart z důvodu upgradování operačního systému][restarts].

Pokud ručně aktualizujete hostovaný operační systém, další informace najdete v tématu [zásady pro vyřazení hostovaného operačního systému][retirepolicy] .

## <a name="guest-os-supportability-and-retirement-policy"></a>Podpora hostovaného operačního systému a zásady vyřazení
Zásady podpory hostovaného operačního systému a zásady vyřazení je vysvětleny [zde][retirepolicy].

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Informační kanál RSS aktualizace operačního systému hosta]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
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
[restarts]: https://docs.microsoft.com/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
[aktuálnější]: https://docs.microsoft.com/azure/cloud-services/applications-dont-support-tls-1-2  

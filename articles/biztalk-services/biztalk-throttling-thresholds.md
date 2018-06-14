---
title: Další informace o omezování ve službě BizTalk Services | Microsoft Docs
description: Další informace o omezování prahové hodnoty a výsledná modul runtime chování služby BizTalk Services. Omezení je na základě využití paměti a počet zpráv. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 39fc5ef36bb581c3a81c9948fda048f6cb75eb7e
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102082"
---
# <a name="biztalk-services-throttling"></a>BizTalk Services: Omezování

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Služba Azure BizTalk Services implementuje služby omezování na základě podmínek, dvě: využití paměti a počtu souběžných zpracování zpráv. Toto téma uvádí omezení prahové hodnoty a popisuje modul Runtime chování, když dojde k omezení podmínku.

## <a name="throttling-thresholds"></a>Omezení šířky pásma prahové hodnoty
Následující tabulka uvádí omezení zdroje a prahové hodnoty:

|  | Popis | Nízké prahové hodnoty | Horní prahové hodnoty |
| --- | --- | --- | --- |
| Memory (Paměť) |% z celkových systémových paměti k dispozici nebo PageFileBytes. <p><p>Celkový počet dostupných PageFileBytes je přibližně 2 časy RAM systému. |60% |70% |
| Zpracování zpráv |Počet současně zpracování zpráv |40 * počet jader |100 * počet jader |

Když je dosaženo horní prahové hodnoty, začne omezení služby Azure BizTalk Services. Omezení zastaví, když je dosaženo nízké prahové hodnoty. Například služby používá 65 % systémové paměti. V takovém případě není omezení služby. Vaše služba spustí pomocí 70 % systémové paměti. V takovém případě služba omezí generovaný a pokračuje v omezení, dokud služba používá 60 % (nízké prahové hodnoty) systémové paměti.

Služba Azure BizTalk Services sleduje stav omezení (normální stav oproti omezenému stavu) a omezení doby trvání.

## <a name="runtime-behavior"></a>Modul runtime chování
Když služba Azure BizTalk Services vstupuje do stavu omezení, dojde k následující položky:

* Omezení je na instanci role. Například:<br/>
  RoleInstanceA je omezení. RoleInstanceB není omezení. V takovém případě zpracování zpráv v RoleInstanceB podle očekávání. Zprávy v RoleInstanceA se zahodí a dojít k následující chybě:<br/><br/>
  **Server je zaneprázdněn. Zkuste to prosím znovu.**<br/><br/>
* Žádné zdroje vyžádání nezadávejte dotazovat nebo stáhnout zprávy. Například:<br/>
  Kanál vrátí zprávy z externího zdroje FTP. Získá instanci role provádění vyžádání do stavu omezení. V takovém případě kanál zastaví stahování další zprávy, dokud role instance zastavení omezení.
* Odpověď je odeslat klientovi, aby klient může znovu odeslat zprávu.
* Musíte počkat, dokud bude omezení je vyřešený. Konkrétně je nutné počkat, dokud nebude dosaženo nízké prahové hodnoty.

## <a name="important-notes"></a>Důležité poznámky
* Omezení nelze zakázat.
* Omezení šířky pásma prahové hodnoty nemůže být upraven.
* Omezení je implementováno celého systému.
* Server databáze SQL Azure má také předdefinované omezení.

## <a name="additional-azure-biztalk-services-topics"></a>Další témata týkající se služby Azure BizTalk Services
* [Instalace služby Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Kurzy: Služby Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Jak začít používat sadu SDK Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Služby Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Viz také
* [BizTalk Services: Developer, Basic, Standard a Premium tabulka edic](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: Tabulka stavů zřízení](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Karty Řídicí panel, Sledování a Škálování](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Zálohování a obnovení](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Název a klíč vystavitele](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>


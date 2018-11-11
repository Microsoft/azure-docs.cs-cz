---
title: Další informace o omezování v BizTalk Services | Dokumentace Microsoftu
description: Další informace o prahových hodnotách omezování a výsledné chování za běhu pro BizTalk Services. Omezení je na základě využití paměti a počet zpráv. MABS, WABS
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
ms.openlocfilehash: daab61a0ea9321b0fb918c60688215c80088e0bc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243347"
---
# <a name="biztalk-services-throttling"></a>BizTalk Services: Omezování

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Služba Azure BizTalk Services implementuje omezení služby založené na dvě podmínky: využití paměti a počet souběžných zpracování zpráv. Toto téma uvádí prahových hodnotách omezování a popisuje chování za běhu, když dojde k omezení podmínku.

## <a name="throttling-thresholds"></a>Prahových hodnotách omezování
Následující tabulka uvádí omezení zdroje a prahové hodnoty:

|  | Popis | Nízké prahové hodnoty | Vysokou prahovou hodnotu |
| --- | --- | --- | --- |
| Memory (Paměť) |% z celkových systémových paměti k dispozici/PageFileBytes. <p><p>Celkový počet dostupných PageFileBytes je přibližně 2 časy RAM systému. |60% |70 % |
| Zpracování zpráv |Počet zpráv souběžně zpracování |40 * počet jader |100 * počet jader |

Po dosažení prahové hodnoty vysoké začne omezení služby Azure BizTalk Services. Omezování zastaví, když je dosaženo nízké prahové hodnoty. Třeba vaše služba používá 65 % systémové paměti. V takovém případě není omezení služby. Vaše služba spustí, pomocí 70 % systémové paměti. V takovém případě služba omezuje a pokračuje v omezení, dokud se služba používá 60 % (nízké prahové hodnoty) systémové paměti.

Služba Azure BizTalk Services sleduje stav omezení (normálním stavu vs. omezené stavu) a omezení doby trvání.

## <a name="runtime-behavior"></a>Chování za běhu
Když Azure BizTalk Services vstupuje do stavu omezení, se stane toto:

* Omezení šířky pásma je za role instance. Příklad:<br/>
  RoleInstanceA omezuje. RoleInstanceB není omezení. V takovém případě se zprávy v RoleInstanceB zpracovávají podle očekávání. Zprávy v RoleInstanceA se zahodí a nezdaří s následující chybou:<br/><br/>
  **Server je zaneprázdněn. Zkuste to prosím znovu.**<br/><br/>
* Žádné zdroje o přijetí změn dotazovat ani stáhnout zprávy. Příklad:<br/>
  Kanál přetáhne zprávy z externího zdroje FTP. Instance role, provádění operace přijetí změn se dostane do stavu omezení. V takovém případě kanál zastaví stahování další zprávy, dokud se role instance zastaví omezení šířky pásma.
* Klient je odeslána odpověď, takže klient může znovu odeslat zprávu.
* Je třeba počkat omezení je vyřešený. Konkrétně musíte počkat, dokud nebude dosaženo nízké prahové hodnoty.

## <a name="important-notes"></a>Důležité poznámky
* Omezení nejde zakázat.
* Prahových hodnotách omezování se nedá upravit.
* Omezení šířky pásma je implementováno celý systém.
* Azure SQL Database Server má také integrované omezování.

## <a name="additional-azure-biztalk-services-topics"></a>Další témata služby Azure BizTalk Services
* [Instalace sady SDK pro Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Kurzy: Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Jak začít používat sadu SDK Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Viz také
* [BizTalk Services: Developer, Basic, Standard a Premium tabulka edic](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: Tabulka stavů zřízení](https://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Karty Řídicí panel, Sledování a Škálování](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Zálohování a obnovení](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Název a klíč vystavitele](https://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>


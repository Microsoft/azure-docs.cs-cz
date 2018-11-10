---
title: Úlohy, které jsou povoleny v různých stavech nebo stavy v BizTalk Services | Dokumentace Microsoftu
description: 'Přípustné v různých MABS stav akce nebo operace: zastavit, spustit, restartovat, pozastavit, obnovit, odstranit, škálovat, aktualizovat konfigurace a základní nahoru'
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
ms.openlocfilehash: bbe1288a42db307001ac778394ac410206f1df21
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228194"
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>Co lze a nelze provést pomocí stav služby BizTalk

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

V závislosti na aktuální stav služby BizTalk se operace, které lze nebo nelze provést na službu BizTalk.

Například můžete zřídit novou službu BizTalk. Po úspěšném provedení, služba BizTalk je v `active` stavu. V aktivním stavu můžete zastavit, pozastavit a odstranění služby BizTalk. Zastavit službu BizTalk a selže zastavit a pak službu BizTalk přejde na `StopFailed` stavu. V `StopFailed` stavu, je možné restartovat službu BizTalk. Pokud se pokusíte operaci, která není povolena, jako je obnovení, dojde k následující chybě:

`Operation not allowed`

## <a name="view-the-possible-states"></a>Zobrazit možné stavy

V následujících tabulkách jsou uvedeny operace nebo akce, které lze provést, pokud se služba BizTalk používá v určitém stavu. ✔ znamená, že operace je povolena v tomto stavu. Prázdná položka znamená, že operaci nejde provést, v tomto stavu.

| Stav služby | Start | Zastavit | Restartování | Pozastavit | Obnovit | Odstranění | Měřítko | Aktualizace <br/> Konfigurace | Backup |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Aktivní |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| Zakázáno |  |  |  |  |  | ✔ | |  |  | 
| Pozastaveno |  |  |  |  | ✔ | ✔ | |  | ✔ |
| Zastaveno | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Nepovedlo se aktualizovat službu |  |  |  |  |  | ✔ | |  |  | 
| DisableFailed |  |  |  |  |  | ✔ | |  |  | 
| EnableFailed |  |  |  |  |  | ✔ | |  |  | 
| StartFailed <br/> StopFailed <br/> RestartFailed | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| SuspendedFailed <br/> ResumeFailed|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| CreatedFailed <br/> RestoreFailed |  |  |  |  |  | ✔ | |  |  | 
| ConfigUpdateFailed  |  |  | ✔ |  |  | ✔ | |✔ | |
| ScaleFailed |  |  |  |  |  | ✔ |✔ | |  |  | 



## <a name="see-also"></a>Viz také
* [Co můžete dělat na kartách řídicí panel, sledování a škálování ve službě BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Co všechno získáte s edicí Developer, Basic, Standard a Premium ve službě BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Postup zálohování a obnovení služby BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Omezení šířky pásma je vysvětleno v BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [Načtení služby Service Bus a řízení přístupu vystavitele název vystavitele klíčových hodnot a pro vaši službu BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Jak začít používat sadu SDK Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)


---
title: Vytvoření a obnovení zálohy ve službě BizTalk Services | Microsoft Docs
description: BizTalk Services zahrnuje zálohování a obnovení. Zjistěte, jak vytvořit a obnovení zálohy a zjistěte, co se zálohuje. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 59f91173-4683-48df-abd5-41262bfce6df
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 45365092f5bcd1a8d309c10404a7437c494a8967
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102337"
---
# <a name="biztalk-services-backup-and-restore"></a>BizTalk Services: Zálohování a obnovení

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Služba Azure BizTalk Services obsahuje funkce zálohování a obnovení. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!NOTE]
> Hybridní připojení se nebudou zálohovány, bez ohledu na verzi. Hybridní připojení, musíte znovu vytvořit.


## <a name="before-you-begin"></a>Než začnete
* Zálohování a obnovení nemusí být dostupné pro všechny edice. V tématu [služby BizTalk Services: Tabulka edic](biztalk-editions-feature-chart.md).
* Zálohování obsahu lze obnovit do stejné služby BizTalk nebo novou službu BizTalk. Chcete-li obnovit službu BizTalk pomocí stejného názvu, musíte odstranit stávající službu BizTalk a název musí být k dispozici. Po odstranění služby BizTalk, může trvat delší dobu, než chtěli pro stejný název, který se má k dispozici. Pokud nemůžete počkat na stejný název být k dispozici, pak obnovte novou službu BizTalk.
* BizTalk Services můžete obnovit do stejné edice, nebo vyšší verze. Obnovení služby BizTalk Services na nižší verzi, z při vytvoření zálohy, není podporováno.
  
    Například lze obnovit zálohu pomocí Základní edice na edici Premium. Zálohování pomocí edice Premium není možné obnovit do Standard Edition.
* Ovládací prvek EDI čísla, která jsou zálohovány zachování kontinuity čísel ovládacího prvku. Pokud po poslední záloze zpracování zpráv, obnovení tohoto obsahu zálohování může způsobit duplicitní řízení čísla.
* Pokud dávce má aktivní zprávy, zpracování dávky **před** spuštěním zálohování. Při vytváření zálohy (jako potřebné nebo plánované), ukládají nikdy zprávy v dávkách. 
  
    **Pokud nedojde k zálohování s active zprávy v dávce, tyto zprávy nejsou zálohovány a proto budou ztraceny.**
* Volitelné: Na portálu služby BizTalk zastavte č. všechny operace správy.

## <a name="create-a-backup"></a>Vytvoření zálohy
Zálohu můžete provést kdykoli a zcela řídí můžete. Chcete-li vytvořit zálohu, použijte [REST API pro správu služby BizTalk Services v Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

## <a name="restore"></a>Obnovení
Chcete-li obnovit zálohu, použijte [REST API pro správu služby BizTalk Services v Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

### <a name="postrestore"></a>Po obnovení ze zálohy
Služba BizTalk je vždy obnovit **pozastaveno** stavu. V tomto stavu můžete provést změny konfigurace před nové prostředí je funkční, včetně:

* Pokud jste vytvořili pomocí sady SDK Azure BizTalk Services aplikace služby BizTalk, budete muset aktualizovat přihlašovací údaje řízení přístupu (ACS) v těchto aplikacích pro práci s prostředím obnovený.
* Obnovení služby BizTalk k replikaci stávajícího prostředí služby BizTalk. V takovém případě-li nakonfigurovat na portálu služby BizTalk Services původní dohody, které používají zdrojové složky FTP, musíte aktualizovat smlouvy v nově obnovený prostředí použít jiné zdrojové složky FTP. Jinak může být dva různé smlouvy pokusu stejnou zprávu pro vyžádání obsahu.
* Pokud jste obnovili tak, aby měl prostředí s více služby BizTalk, zkontrolujte, zda že cílíte správné prostředí aplikace Visual Studio, rutiny prostředí PowerShell, rozhraní REST API nebo Trading Partner OM rozhraní API pro správu.
* Je vhodné nakonfigurovat automatické zálohování na nově obnovený prostředí služby BizTalk.

## <a name="what-gets-backed-up"></a>Co se zálohuje
Při vytvoření zálohy, budou zálohovány následující položky:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Zálohovaných položek</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portál Azure BizTalk Services</strong></td>
</tr> 
<tr>
<td>Konfigurace a prostředí Runtime</td> 
<td>
<ul>
<li>Podrobnosti o partnera a profilu</li>
<li>Partner smlouvy</li>
<li>Vlastní sestavení, nasazení</li>
<li>Mosty nasazení</li>
<li>Certifikáty</li>
<li>Transformace nasazení</li>
<li>Kanály</li>
<li>Šablony vytvořili a uložili na portálu služby BizTalk</li>
<li>X12 mapování ST01 a GS01</li>
<li>Ovládací prvek čísla (EDI)</li>
<li>Hodnoty AS2 zprávy povinná kontrola úrovně Důvěryhodnosti</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Služba Azure BizTalk</strong></td>
</tr> 
<tr>
<td>Certifikát SSL</td> 
<td>
<ul>
<li>Data certifikátu SSL</li>
<li>Heslo certifikátu SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Nastavení služby BizTalk</td> 
<td>
<ul>
<li>Počet jednotek škálování</li>
<li>Edice</li>
<li>Verze produktu</li>
<li>Oblast nebo Datacenter</li>
<li>Ovládací prvek Service (ACS) přístup k oboru názvů a klíče</li>
<li>Sledování připojovací řetězec databáze</li>
<li>Archivace připojovací řetězce k účtu úložiště</li>
<li>Monitorování připojovací řetězce k účtu úložiště</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Další položky</strong></td>
</tr> 
<tr>
<td>Sledování databáze</td> 
<td>Při vytváření služby BizTalk jsou zadat podrobnosti sledování databáze, včetně serveru Azure SQL Database a název databáze sledování. Sledování databázi není automaticky zálohovat.
<br/><br/>
<strong>Důležité upozornění</strong><br/>
Pokud se odstraní databázi sledování a potřeby databáze obnovena, musí existovat předchozí zálohy. Pokud zálohu neexistuje, databázi sledování a jeho data nejsou použitelná pro obnovení. V této situaci se vytvořte novou databázi pro sledování se stejným názvem databáze. Doporučuje se geografická replikace.</td>
</tr> 
</table>

## <a name="next"></a>Další
Vytvoření služby Azure BizTalk Services, přejděte na [BizTalk Services: zřízení](http://go.microsoft.com/fwlink/p/?LinkID=302280). Pokud chcete začít vytvářet aplikace, přejděte na článek [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Viz také
* [Zálohování služby BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Služba BizTalk obnovit ze zálohy](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk Services: Developer, Basic, Standard a Premium tabulka edic](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk Services: zřízení](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Tabulka stavů zřízení](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Karty Řídicí panel, Sledování a Škálování](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Omezování](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Název a klíč vystavitele](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Jak začít používat sadu SDK Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png


---
title: Vytvoření a obnovení ze zálohy v BizTalk Services | Dokumentace Microsoftu
description: BizTalk Services zahrnuje zálohování a obnovení. Další informace o vytvoření a obnovení zálohy a zjistit, co se zálohuje. MABS, WABS
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
ms.openlocfilehash: 6dc3fb97c912aa9ac66e3d40a8a0318a6938905c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230287"
---
# <a name="biztalk-services-backup-and-restore"></a>BizTalk Services: Zálohování a obnovení

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Služba Azure BizTalk Services obsahuje funkce zálohování a obnovení. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!NOTE]
> Hybridní připojení se nebudou zálohovány, bez ohledu na to, na edici. Hybridní připojení, musíte znovu vytvořit.


## <a name="before-you-begin"></a>Než začnete
* Zálohování a obnovení nemusí být dostupné pro všechny edice. Zobrazit [BizTalk Services: Tabulka edic](biztalk-editions-feature-chart.md).
* Zálohování obsahu je možné obnovit ve stejné službě BizTalk nebo na novou službu BizTalk. Pokud chcete obnovit službu BizTalk pomocí stejného názvu, musí odstranění stávající služby BizTalk a název musí být k dispozici. Po odstranění služby BizTalk, může trvat déle, než potřebovala pro stejný název bude k dispozici. Pokud nemůžete počkat stejným názvem, aby se k dispozici, obnovte novou službu BizTalk.
* BizTalk Services je možné obnovit do stejné edice, nebo vyšší verze. Obnovení služby BizTalk Services na nižší verzi, od kdy bylo provedeno zálohování, není podporováno.
  
    Například je možné obnovit zálohu pomocí edice Basic na edici Premium. Nelze obnovit zálohu pomocí edice Premium, na edici Standard.
* K zachování kontinuity podnikových procesů kontrolních čísel se zálohují EDI kontrolních čísel. Pokud po poslední záloze po zpracování zprávy se obnovení tohoto obsahu zálohování může způsobit duplicitní kontrolní čísla.
* Pokud batch má aktivní zprávy, zpracování dávky **před** zrovna zálohovat. Při vytváření zálohy (jako potřebné ani do plánovaných), se nikdy neukládají zprávy v dávkách. 
  
    **Pokud je záloha aktivní zpráv v dávce, tyto zprávy nejsou zálohovány a proto budou ztraceny.**
* Volitelné: Portálu BizTalk Services, zastavte všechny operace správy.

## <a name="create-a-backup"></a>Vytvoření zálohy
Zálohu můžete provést kdykoli a zcela řídí vám. Chcete-li vytvořit zálohu, použijte [rozhraní REST API pro správu služby BizTalk Services v Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

## <a name="restore"></a>Obnovení
Chcete-li obnovit zálohu, použijte [rozhraní REST API pro správu služby BizTalk Services v Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

### <a name="postrestore"></a>Po obnovení zálohy
Služba BizTalk je vždy obnoví **pozastaveno** stavu. V tomto stavu můžete provést změny konfigurace předtím, než je funkční, včetně nové prostředí:

* Pokud jste vytvořili aplikace služby BizTalk pomocí sady SDK Azure BizTalk Services, budete muset aktualizovat přihlašovací údaje řízení přístupu (ACS) v těchto aplikacích pro práci s obnovenou prostředí.
* Obnovení služby BizTalk pro replikaci stávající prostředí služby BizTalk. V této situaci pokud existují smlouvy nakonfigurované v původní portálu BizTalk Services, které používají zdrojové složky FTP, budete muset aktualizovat smluv v nově obnovenou prostředí použít jiné zdrojové složce FTP. V opačném případě mohou existovat dva různé smlouvy pokusu pull stejné zprávy.
* Pokud jste obnovili jste více prostředí služby BizTalk, zkontrolujte, zda že cílíte správné prostředí v aplikacích Visual Studio, rutin prostředí PowerShell, rozhraní REST API nebo Trading Partner objektový model rozhraní API pro správu.
* Je vhodné nakonfigurovat automatizovaných záloh v prostředí nově obnovenou službu BizTalk.

## <a name="what-gets-backed-up"></a>Co se zálohuje
Po vytvoření zálohy se zálohují následující položky:

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
<td>Konfigurace a modulu Runtime</td> 
<td>
<ul>
<li>Podrobnosti o partnerovi a profilu</li>
<li>Partner smlouvy</li>
<li>Vlastní sestavení, nasazení</li>
<li>Mosty nasazení</li>
<li>Certifikáty</li>
<li>Transformace nasazení</li>
<li>Kanály</li>
<li>Šablony vytvořili a uložili v portálu BizTalk Services</li>
<li>X12 ST01 a GS01 mapování</li>
<li>Kontrolní čísla (EDI)</li>
<li>Hodnoty MIC zprávy AS2</li>
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
<li>Oblast/datové centrum</li>
<li>Obor názvů služby Access Control Service (ACS) a klíč</li>
<li>Sledování připojovací řetězec databáze</li>
<li>Archivace připojovací řetězec účtu úložiště</li>
<li>Monitorování připojovací řetězec účtu úložiště</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Další položky</strong></td>
</tr> 
<tr>
<td>Sledování databáze</td> 
<td>Při vytvoření služby BizTalk jsou zadat podrobnosti sledování databáze, včetně Azure SQL Database Server a název databáze pro sledování. Sledování databáze není automaticky zálohována.
<br/><br/>
<strong>Důležité upozornění</strong><br/>
Pokud se odstraní databázi sledování a potřeby databáze obnovena, musí existovat předchozí zálohy. Pokud zálohu neexistuje, nejsou databázi sledování a její data obnovit. V takovém případě vytvořte novou databázi sledování se stejným názvem databáze. Doporučuje se geografická replikace.</td>
</tr> 
</table>

## <a name="next"></a>Další
Vytvoření služby Azure BizTalk Services, přejděte na [BizTalk Services: zřízení](https://go.microsoft.com/fwlink/p/?LinkID=302280). Pokud chcete začít vytvářet aplikace, přejděte na článek [Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Viz také
* [Zálohování služby BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Obnovení ze zálohy služby BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk Services: Developer, Basic, Standard a Premium tabulka edic](https://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk Services: zřízení](https://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Tabulka stavů zřízení](https://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Karty Řídicí panel, Sledování a Škálování](https://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Omezování](https://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Název a klíč vystavitele](https://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Jak začít používat sadu SDK Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png


---
title: Vytvoření služby Azure BizTalk Services na webu Azure Portal | Dokumentace Microsoftu
description: Naučte se na webu Azure Portal zřizovat nebo vytvářet službu Azure BizTalk Services; MABS, WABS.
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: fe56b84b881b2afce9da9a7f7dfe4637c3494aa8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261957"
---
# <a name="create-biztalk-services-using-the-azure-portal"></a>Vytvoření služby BizTalk Services pomocí webu Azure Portal

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!TIP]
> Abyste se mohli přihlásit na webu Azure Portal, potřebujete účet Azure a předplatné Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podívejte se na stránku [bezplatné zkušební verze Azure](https://go.microsoft.com/fwlink/p/?LinkID=239738).


## <a name="CreateService"></a>Vytvoření služby BizTalk

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

V závislosti na stavu služby BizTalk se může stát, že se některé operace nebudou dát provést. Seznam těchto operací najdete v článku [BizTalk Services: Tabulka stavů](biztalk-service-state-chart.md).

## <a name="post-provisioning-steps"></a>Kroky pro zřízení
* [Instalace certifikátu na místním počítači](#InstallCert)
* [Přidání certifikátu pro produkční prostředí](#AddCert)
* [Získání oboru názvů řízení přístupu](#ACS)

#### <a name="InstallCert"></a>Instalace certifikátu na místním počítači

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="AddCert"></a>Přidání certifikátu pro produkční prostředí

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="ACS"></a>Získání oboru názvů řízení přístupu

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Tento obor názvů řízení přístupu zadáváte při nasazení projektu služby BizTalk v sadě Visual Studio. Obor názvů řízení přístupu se pro vaši službu BizTalk vytvoří automaticky.

Hodnoty řízení přístupu se dají použít s libovolnou aplikací. Při vytváření služby Azure BizTalk Services tento obor názvů řízení přístupu řídí ověřování ve vašem nasazení služby BizTalk. Pokud chcete změnit předplatné nebo spravovat obor názvů, v levém navigačním podokně vyberte **ACTIVE DIRECTORY** a potom zvolte svůj obor názvů. Možnosti jsou uvedené na hlavním panelu.

Kliknutím na **Manage** (Správa) otevřete portál pro správu řízení přístupu. Na portálu pro správu řízení přístupu používá služba BizTalk **identity služby**:  
![Identita služby ACS na portálu pro správu řízení přístupu][ACSServiceIdentities]

Identita služby Access Control Service je sada přihlašovacích údajů, která umožňuje aplikacím nebo klientům přímé ověření u řízení přístupu a získání tokenu.

> [!IMPORTANT]
> Služba BizTalk používá výchozí identitu služby **Vlastník** a hodnotu **Heslo**. Pokud místo hodnoty Heslo použijete hodnotu Symetrický klíč, může dojít k následující chybě.<br/><br/>*Zadané přihlašovací údaje neumožňují připojení k účtu služby pro správu řízení přístupu.*
> 
> 

V článku [Správa oboru názvů služby ACS](https://msdn.microsoft.com/library/azure/hh674478.aspx) najdete některé pokyny a doporučení.

## <a name="requirements-explained"></a>Vysvětlení požadavků
Tyto požadavky se netýkají edice Free.

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Co potřebujete?</strong></td>
        <td><strong>Proč to potřebujete?</strong></td>
</tr>
<tr>
<td>Předplatné Azure</td>
<td>Předplatné určuje, kdo se může přihlásit k Azure. Držitel účtu vytvoří předplatné na stránce <a HREF="https://account.windowsazure.com/Subscriptions">předplatných Azure</a>.
<br/><br/>
Účet Azure může obsahovat víc předplatných a může ho spravovat každý, kdo k tomu má oprávnění. Váš držitel účtu Azure například vytvoří předplatné s názvem <em>Predplatne_sluzby_BizTalk</em> a dá k tomuto předplatnému přístup správcům služby BizTalk z vaší společnosti (třeba ContosoBTSAdmins@live.com). V tomto scénáři budou mít správci služby BizTalk po přihlášení k Azure úplná práva správce ke všem hostovaným službám v daném předplatném, včetně služby Azure BizTalk Services. Správci služby BizTalk nejsou držiteli účtu Azure, takže nemají přístup k informacím o fakturaci.
<br/><br/>Další informace najdete v článku 
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=267577">Správa předplatných a účtů úložiště v Azure</a>.
</td>
</tr>
<tr>
<td>Azure SQL Database</td>
<td>Slouží k ukládání tabulek, zobrazení a uložených procedur používaných službou BizTalk, včetně dat sledování.
<br/><br/>
Když vytváříte službu BizTalk, můžete použít existující server SQL Azure nebo službu Azure SQL Database nebo můžete automaticky vytvořit nový server nebo databázi.
<br/><br/>
Škálování služby SQL Database se konfiguruje automaticky. Pro službu BizTalk většinou stačí výchozí škálování. Změna škálování má vliv na ceny. Další informace najdete v tématu <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=234930">o účtech a cenách služby Azure SQL Database</a>
.<br/><br/>
<strong>Poznámky</strong>
<br/>
<ul>
<li> Při vytváření nového serveru SQL Azure a služby SQL Database automaticky dojde k povolení služeb Azure. Služba BizTalk vyžaduje povolení služeb Azure.</li>
<li>Pokud vytvoříte novou službu Azure SQL Database na existujícím serveru SQL Azure, pravidla brány firewall serveru se nezmění. V důsledku toho je možné, že jiné služby Azure nebudou mít přístup k databázím serveru.</li>
</ul>
</td>
</tr>
<tr>
<td>Obor názvů řízení přístupu</td>
<td>Ověřuje se u služby Azure BizTalk Services. Tento obor názvů řízení přístupu zadáváte při nasazení projektu služby BizTalk v sadě Visual Studio. Při vytváření služby BizTalk se obor názvů řízení přístupu vytvoří automaticky.</td>
</tr>

<tr>
<td>Účet služby Azure Storage</td>
<td>Poskytuje přístup k tabulkám, objektům blob a frontám, které vaše služba BizTalk používá k ukládání těchto informací:

<ul>
<li>Soubory protokolu, které monitorují službu BizTalk. </li>
<li>Při vytváření smlouvy X12 nebo AS2 mezi partnery můžete povolit funkci archivace, která umožňuje ukládat vlastnosti správ. Tato data se ukládají na účet služby Storage.</li>
</ul>
<br/>
Při vytváření služby BizTalk můžete použít stávající účet služby Storage nebo automaticky vytvořit nový.
<br/><br/>
Pro službu BizTalk stačí výchozí nastavení služby Storage.
<br/><br/>
Při vytváření účtu služby Storage se automaticky vytvoří primární a sekundární klíč. Tyto klíče řídí přístup k vašemu účtu služby Storage. Služba BizTalk automaticky používá primární klíč.
<br/><br/>
Další informace najdete v článku o <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=285671">službě Storage</a>.
</td>
</tr>

<tr>
<td>Privátní certifikát SSL</td>
<td>
Při vytváření služby Azure BizTalk se vytvoří také adresa URL HTTPS, která obsahuje název vaší služby BizTalk. Tato adresa URL je automaticky nakonfigurovaná tak, aby používala certifikát podepsaný svým držitelem určený jenom pro vývojové prostředí. V produkčním prostředí budete potřebovat privátní certifikát SSL.
<br/><br/>
<strong>Důležité informace o certifikátu SSL</strong>

<ul>
<li>Datum vypršení platnosti certifikátu musí být za dobu kratší než 5 let.</li>
<li>Všechny privátní certifikáty vyžadují heslo. Heslo si zapamatujte a jako osvědčený postup také doporučujeme ho sdělit vašim správcům.</li>
<li>Certifikáty podepsané svým držitelem se používají v testovacím/vývojovém prostředí. Pokud používáte certifikáty podepsané svým držitelem, importujte certifikát do úložiště osobních certifikátů a do úložiště důvěryhodných kořenových certifikačních autorit.</li>
</ul>
<br/>Při odesílání požadavku na certifikát pro produkční prostředí vaší certifikační autoritě uveďte následující vlastnosti certifikátu:
<br/>

<ul>
<li><strong>Rozšířené použití klíče</strong>: Služba Azure BizTalk Services vyžaduje minimálně ověření serveru.</li>
<li><strong>Běžný název</strong>: Zadejte plně kvalifikovaný název domény (FQDN) adresy URL služby Azure BizTalk. Projděte si část <a HREF="#CreateService">Vytvoření služby BizTalk</a> v tomto článku.</li>
</ul>
<br/>
Po vytvoření služby BizTalk můžete přidat nový nebo jiný certifikát.
</td>
</tr>
</table>
<!---Loc Comment: Please, check link [Create a BizTalk Service] since it is not redirecting to any location.--->



## <a name="hybrid-connections"></a>Hybridní připojení
Při vytváření služby BizTalk Azure je dostupná karta **Hybridní připojení**:

![Karta Hybridní připojení][HybridConnectionTab]

Hybridní připojení slouží k připojení webu Azure nebo mobilní služby Azure k jakémukoli místnímu prostředku, který používá statický port TCP, jako je třeba SQL Server, MySQL, webové rozhraní API HTTP, Mobile Services a většina vlastních webových služeb.  Hybridní připojení se liší od služby BizTalk Adapter Service. Služba BizTalk Adapter Service se používá k připojení služby Azure BizTalk Services k místnímu obchodnímu systému (LOB).

 Další informace, třeba o vytváření a správě hybridních připojení, najdete v článku [Hybridní připojení](integration-hybrid-connection-overview.md).

## <a name="next-steps"></a>Další kroky
Služba BizTalk je teď vytvořená a vy se můžete seznámit s různými kartami: [BizTalk Services: Karty Řídicí panel, Sledování a Škálování](biztalk-dashboard-monitor-scale-tabs.md). Služba BizTalk je připravená pro vaše aplikace. Pokud chcete začít vytvářet aplikace, přejděte na článek [Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Viz také
* [BizTalk Services: Tabulka edic](biztalk-editions-feature-chart.md)<br/>
* [BizTalk Services: Tabulka stavů](biztalk-service-state-chart.md)<br/>
* [BizTalk Services: Zálohování a obnovení](biztalk-backup-restore.md)<br/>
* [BizTalk Services: Omezování](biztalk-throttling-thresholds.md)<br/>
* [BizTalk Services: Název a klíč vystavitele](biztalk-issuer-name-issuer-key.md)<br/>
* [Jak začít používat sadu SDK Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Hybridní připojení](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png

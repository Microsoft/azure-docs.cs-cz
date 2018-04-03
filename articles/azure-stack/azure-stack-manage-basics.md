---
title: Základy správy Azure zásobníku | Microsoft Docs
description: Zjistěte, co potřebujete vědět, abyste mohli spravovat Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mabrigg
ms.openlocfilehash: df4a5a17ad034ae5d6ab82791c020634a8758b71
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-administration-basics"></a>Základy správy Azure zásobníku
Existuje několik věcí, které potřebujete vědět, pokud začínáte Azure zásobníku správy. V tomto návodu najdete přehled role jako operátor zásobník Azure a co je potřeba říct uživatelům pro ně k produktivní rychle.

## <a name="understand-the-builds"></a>Pochopení sestavení

### <a name="integrated-systems"></a>Integrované systémy

Pokud používáte Azure zásobníku integrované systému, aktualizovaných verzí Azure zásobníku se distribuují prostřednictvím balíčky aktualizací. Můžete importovat tyto balíčky a použít je s využitím dlaždici aktualizace na portálu správce.
 
### <a name="development-kit"></a>Development kit

Pokud používáte Azure zásobníku Development Kit, přečtěte si [co je Azure zásobníku?](.\asdk\asdk-what-is.md) článek a ujistěte se, pochopit účel development kit a její omezení. Měli byste použít development kit jako "izolovaném prostoru," kde může vyhodnotit Azure zásobníku a vývoj a testování vaší aplikace v testovacím prostředí. (Informace o nasazení naleznete v tématu [nasazení Azure zásobníku Development Kit](.\asdk\asdk-deploy.md) kurzu.)

Jako je například Azure jsme inovacemi. Zajistěte rychle. Jsme budete pravidelně sestavení pro vydání nové. Pokud používáte sadě pro vývoj a chcete přesunout na nejnovější verzi, musíte [znovu nasadit Azure zásobníku](.\asdk\asdk-redeploy.md). Nelze použít balíčky aktualizací. Tento proces trvá určitou dobu, ale výhodou je, že můžete vyzkoušet nejnovější funkce. Dokumentaci k sadě development kit na našem webu odráží nejnovější verzi sestavení.

## <a name="learn-about-available-services"></a>Další informace o dostupných služeb

Budete potřebovat sledování služeb, které můžete zpřístupnit uživatelům. Azure zásobníku podporuje podmnožinu služeb Azure. Seznam podporovaných služeb budou nadále momentální.

**Základní služby**

Ve výchozím nastavení, Azure Stack zahrnuje následující "základní služby" při nasazení Azure zásobníku:

- Compute
- Úložiště
- Sítě
- Key Vault

S těmito službami základní nabízejí uživatelům s minimální konfigurací infrastruktury jako služba (IaaS).

**Další služby**

V současné době podporujeme tyto další služby platformy jako služba (PaaS):

- App Service
- Azure Functions
- Databáze SQL a MySQL

Tyto služby vyžadují další konfiguraci, než jste je zpřístupnit uživatelům. Další informace najdete v tématu "Kurzy" a "postupy guides\Offer služby" části naší dokumentaci operátor zásobník Azure.

**Přehled služby**

Azure zásobníku budou nadále přidat podporu pro služby Azure. Předpokládané plán, najdete v článku [Azure zásobník: rozšíření Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) dokument White Paper. Také můžete monitorovat [příspěvcích na blogu Azure zásobníku](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) pro nové oznámení.

## <a name="what-account-should-i-use"></a>Který účet mám použít?
Existuje několik, které byste měli vědět při správě Azure zásobníku důležité informace o účtu. Zejména v nasazení používáte Windows Server Active Directory Federation Services (AD FS) jako zprostředkovatele identity místo Azure Active Directory (Azure AD). Zásobník Azure integrované systémy a ASDK nasazení, platí následující aspekty účet:


|Účet|Azure AD|AD FS|
|-----|-----|-----|
|Místní správce (. \Administrator)|Správce hostitele ASDK|Správce hostitele ASDK|
|AzureStack\AzureStackAdmin|Správce hostitele ASDK<br><br>Lze použít pro přihlášení k portálu pro správu Azure zásobníku<br><br>Přístup k zobrazení a správě okruhy Service Fabric|Správce hostitele ASDK<br><br>Žádný přístup k portálu pro správu Azure zásobníku<br><br>Přístup k zobrazení a správě okruhy Service Fabric<br><br>Už vlastníkem služby výchozí zprostředkovatel předplatné (distribučních bodů)|
|AzureStack\CloudAdmin|Můžete používat a spuštění povolených příkazů v rámci privilegované koncový bod|Můžete používat a spuštění povolených příkazů v rámci privilegované koncový bod<br><br>Nemůže přihlásit k hostiteli ASDK<br><br>Vlastník předplatného poskytovatele výchozí (distribučních bodů)|
|Globální správce Azure AD|Použít během instalace<br><br>Vlastník předplatného poskytovatele výchozí (distribučních bodů)|Neuvedeno|
|

## <a name="what-tools-do-i-use-to-manage"></a>Jaké nástroje lze použít ke správě?
 
Můžete použít [portálu správce](azure-stack-manage-portals.md) nebo prostředí PowerShell pro správu Azure zásobníku. Nejjednodušší způsob, jak Seznamte se základními koncepty je prostřednictvím portálu. Pokud chcete pomocí prostředí PowerShell, nejsou přípravné kroky. Je nutné [nainstalovat](azure-stack-powershell-install.md) prostředí PowerShell, [Stáhnout](azure-stack-powershell-download.md) další moduly a [konfigurace](azure-stack-powershell-configure-admin.md) prostředí PowerShell.

Azure zásobníku používá jako podkladový mechanismus nasazení, správu a organizace Azure Resource Manager. Pokud se chystáte spravovat Azure zásobníku a podporu pro uživatele, měli byste získat informace o službě Správce prostředků. Najdete v článku [Začínáme s Azure Resource Manager](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) dokument White Paper.

## <a name="your-typical-responsibilities"></a>Vaše odpovědnosti typické

Vaši uživatelé chtějí používat služby. Z perspektivy je vaše hlavní role Chcete-li tyto služby jsou pro ně dostupné. Musíte rozhodnout, které služby nabízet a zpřístupněte těchto služeb vytvořením plány, nabídky a kvóty. Další informace najdete v tématu [přehled nabídky služeb v zásobníku Azure](azure-stack-offer-services-overview.md). 

Také budete muset přidat položky do [marketplace](azure-stack-marketplace.md), například bitové kopie virtuálních počítačů. Nejjednodušším způsobem je [stáhnout položky marketplace z Azure do Azure zásobníku](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Pokud chcete testovat vaše plány, nabídky a služby, měli byste použít [portálu user portal](azure-stack-manage-portals.md); není správce portálu.

Kromě poskytování služeb, je nutné provést všechny regulární povinností operátoru zachovat zásobník Azure spuštěná. Tyto činnosti zahrnují následující:

- Přidání uživatelských účtů (pro [Azure Active Directory](azure-stack-add-new-user-aad.md) nasazení nebo [Active Directory Federation Services](azure-stack-add-users-adfs.md) nasazení)
- [Přiřazení role (RBAC) řízení přístupu podle rolí](azure-stack-manage-permissions.md) (Toto není omezen na správce.)
- [Monitorování stavu infrastruktury](azure-stack-monitor-health.md)
- Spravovat [sítě](azure-stack-viewing-public-ip-address-consumption.md) a [úložiště](azure-stack-manage-storage-accounts.md) prostředky
- Nahradí chybný hardwaru, například [poškozený disk nahraďte](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Co říct uživatelům

Je potřeba dát uživatelům vědět, jak pracovat se službami v zásobníku Azure, jak se připojit k prostředí a postupy k odběru nabízí. Kromě jakékoli vlastní dokumentaci, můžete chtít poskytovat uživatelům, můžete nastavit uživatele na web Azure zásobníku uživatelé dokumentaci.

**Pochopit, jak pracovat se službami v Azure zásobníku**

Není k dispozici informace, které uživatelé musí pochopit předtím, než použití služeb a vytvářet aplikace v Azure zásobníku. Například jsou konkrétní požadavky na verzi prostředí PowerShell a rozhraní API. K dispozici je také některé funkce rozdílů mezi službou v Azure a ekvivalentní služby v Azure zásobníku. Zkontrolujte vaši uživatelé v těchto článcích:

- [Klíčové aspekty: pomocí služby nebo vytváření aplikací pro Azure zásobníku](user/azure-stack-considerations.md)
- [Důležité informace pro virtuální počítače v Azure zásobníku](user/azure-stack-vm-considerations.md)
- [Úložiště: rozdíly a aspekty](user/azure-stack-acs-differences.md)

Informace v těchto článcích shrnuje rozdíly mezi Azure zásobníku a služby v Azure. Doplňuje informace, které jsou k dispozici pro službu Azure v globální dokumentace k Azure.

**Připojení k Azure zásobníku jako uživatel**

Ve vývojovém prostředí sady Pokud uživatel nemá přístup ke vzdálené ploše na development kit hostitele, se musíte nakonfigurovat připojení virtuální privátní sítě (VPN) předtím, než získají přístup k Azure zásobníku. V tématu [připojit k Azure zásobníku](azure-stack-connect-azure-stack.md). 

Vaši uživatelé budou chtít vědět, jak [přístup k portálu user portal ](user/azure-stack-use-portal.md) nebo jak se připojit pomocí prostředí PowerShell. V prostředí, integrované systémy se liší adresy portálu uživatele na jedno nasazení. Bude nutné uživatelům poskytnout správnou adresu URL.

Pokud používáte PowerShell, uživatelé mohou mít registrace poskytovatele prostředků, před použitím služby. (Zprostředkovatel prostředků spravuje služby. For example, sítí poskytovatele prostředků spravuje prostředky, například virtuálních sítí, síťových rozhraní a nástroje pro vyrovnávání zatížení.) Musí [nainstalovat](user/azure-stack-powershell-install.md) prostředí PowerShell, [Stáhnout](user/azure-stack-powershell-download.md) další moduly a [konfigurace](user/azure-stack-powershell-configure-user.md) prostředí PowerShell (která zahrnuje registrace zprostředkovatele prostředků).

**Přihlášení k odběru nabídky**

Předtím, než uživatel mají přístup ke službám, musí [přihlášení k odběru na nabídku](azure-stack-subscribe-plan-provision-vm.md) , kterou jste vytvořili jako operátor.

## <a name="where-to-get-support"></a>Kde získat podporu

### <a name="integrated-systems"></a>Integrované systémy

Pro integrovaný systém je koordinované Eskalace a procesu překladu, který mezi společností Microsoft a našimi hardwarovými partnery výrobce (OEM).

Pokud nastane problém s cloud services, se nabízí podporu prostřednictvím Microsoft podporu služby zákazníkům (CSS). Klikněte na ikonu nápovědy a podpory (otazník) v pravém horním rohu portálu správce, a pak klikněte na tlačítko **nová žádost o podporu**, otevře se lokality, kde můžete otevřít přímo žádost o podporu.

Pokud nastane problém s nasazením, opravy a aktualizace, hardwaru (včetně replaceable jednotky pole) a veškerý software značkou hardwaru, jako je například software běžící v hostiteli životního cyklu hardwaru, obraťte se na dodavatele hardwaru, od výrobců OEM nejdřív.

Pro jiné účely kontaktujte CSS společnosti Microsoft.

### <a name="development-kit"></a>Development kit

Pro development kit můžete klást otázky týkající se podpory v [fóra Microsoft](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Klikněte na ikonu nápovědy a podpory (otazník) v pravém horním rohu portálu správce, a pak klikněte na tlačítko **nová žádost o podporu**, přímo otevře webu fóra. Tato fóra jsou pravidelně sledovat. Protože development kit je prostředí pro testování, neexistuje oficiální podpora nabízeným přes Microsoft CSS.

## <a name="next-steps"></a>Další postup

- [Oblast správy v Azure zásobníku](azure-stack-region-management.md)



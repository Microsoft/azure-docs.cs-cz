---
title: Základy správy Azure Stack | Dokumentace Microsoftu
description: Zjistěte, co potřebujete vědět o správě služby Azure Stack.
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
ms.date: 06/08/2018
ms.author: mabrigg
ms.openlocfilehash: 39dc48b1357b07932efbccaf002f54ca58edae24
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35899014"
---
# <a name="azure-stack-administration-basics"></a>Základy správy Azure Stack
Existuje několik věcí, které je potřeba vědět, pokud je pro vás nová pro správu služby Azure Stack. Tento návod poskytuje přehled o vaší role jako jejich operátory Azure stacku a co je potřeba říct uživatelům pro ně a být tak produktivní rychle.

## <a name="understand-the-builds"></a>Vysvětlení sestavení

### <a name="integrated-systems"></a>Integrované systémy

Pokud používáte systémech pro Azure Stack integrované, aktualizované verze služby Azure Stack se distribuují prostřednictvím balíčků aktualizace. Můžete importovat tyto balíčky a použít je s použitím aktualizací dlaždice na portálu pro správce.
 
### <a name="development-kit"></a>Vývojová sada

Pokud používáte Azure Stack Development Kit, přečtěte si [co je Azure Stack?](.\asdk\asdk-what-is.md) článek a ujistěte se, že pochopit účel development kit a jaká jsou její omezení. Vývojová sada byste měli používat jako "izolovaném prostoru," kde můžete vyhodnotit Azure Stack a vyvíjet a testovat své aplikace v neprodukčním prostředí. (Informace o nasazení, najdete v článku [nasazení Azure Stack Development Kit](.\asdk\asdk-install.md) článku.)

Jako je Azure můžeme inovovat rychle. Pravidelně vydáváme nové buildy. Pokud používáte development kit a chcete přesunout na nejnovější verzi, je nutné [opětovné nasazení Azure Stack](.\asdk\asdk-redeploy.md). Balíčky aktualizací nelze použít. Tento proces trvá určitou dobu, ale má výhodu, že budete moct vyzkoušet nejnovější funkce. Dokumentaci development kit na našem webu odráží nejnovější sestavení pro vydání.

## <a name="learn-about-available-services"></a>Další informace o dostupných služeb

Budete potřebovat povědomí o služby, které můžete zpřístupnit uživatelům. Azure Stack podporuje podmnožinu služeb Azure. Seznam podporovaných služeb bude dál vyvíjejí.

**Základní služby**

Ve výchozím nastavení, služby Azure Stack zahrnuje následující "základní služby" při nasazení Azure Stack:

- Compute
- Úložiště
- Sítě
- Key Vault

Pomocí těchto základních služeb můžou nabízet Infrastructure-as--Service (IaaS) uživatelům s minimální konfigurací.

**Další služby**

V současné době Podporujeme následující další služby Platform-as-a-Service (PaaS):

- App Service
- Azure Functions
- Databáze SQL a MySQL

Tyto služby vyžadují další konfiguraci, předtím, než můžete zpřístupnit je pro vaše uživatele. Další informace najdete v tématu "Kurzy" a "s postupy guides\Offer služby" oddílů část naší dokumentace operátor Azure stacku.

**Plán služby**

Azure Stack bude pokračovat a přidat podporu pro služby Azure. Předpokládané plán služby, najdete v článku [Azure Stack: rozšíření Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) dokument White Paper. Také můžete monitorovat [příspěvcích na blogu Azure Stack](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) pro nové oznámení.

## <a name="what-account-should-i-use"></a>Jaký účet mám použít?
Existuje několik důležitých informací účtu, které byste měli vědět při správě služby Azure Stack. Zejména v nasazeních pomocí systému Windows Server Active Directory Federation Services (AD FS) jako poskytovatel identit Azure Active Directory (Azure AD). Integrované systémy Azure Stack a ASDK nasazení, platí následující aspekty účtu:


|Účet|Azure AD|AD FS|
|-----|-----|-----|
|Místní správce (. \Administrator)|Správce ASDK hostitele|Správce ASDK hostitele|
|AzureStack\AzureStackAdmin|Správce ASDK hostitele<br><br>Je možné se přihlásit na portál pro správu služby Azure Stack<br><br>Přístup k zobrazení a správě okruhů Service Fabric|Správce ASDK hostitele<br><br>Žádný přístup k portálu pro správu služby Azure Stack<br><br>Přístup k zobrazení a správě okruhů Service Fabric<br><br>Už vlastníka z výchozí zprostředkovatel předplatného (službou Device Provisioning)|
|AzureStack\CloudAdmin|Můžete používat a povolených příkazů v rámci privilegovaných koncového bodu|Můžete používat a povolených příkazů v rámci privilegovaných koncového bodu<br><br>Nemůžete se přihlásit k hostiteli ASDK<br><br>Výchozí zprostředkovatel předplatného (DPS)|
|Globální správce Azure AD|Použít během instalace<br><br>Výchozí zprostředkovatel předplatného (DPS)|Neuvedeno|
|

## <a name="what-tools-do-i-use-to-manage"></a>Jaké nástroje použít ke správě?
 
Můžete použít [portálu správce](azure-stack-manage-portals.md) nebo prostředí PowerShell pro správu služby Azure Stack. Nejjednodušší způsob, jak informace o základních konceptech je prostřednictvím portálu. Pokud chcete použít PowerShell, jsou přípravné kroky. Je nutné [nainstalovat](azure-stack-powershell-install.md) prostředí PowerShell, [Stáhnout](azure-stack-powershell-download.md) dalších modulů, a [konfigurace](azure-stack-powershell-configure-admin.md) prostředí PowerShell.

Azure Stack jako základní mechanismus nasazování, správu a organizace používá Azure Resource Manageru. Pokud budete ke správě služby Azure Stack a bylo možné podporovat uživatele, měli byste získat informace o Resource Manageru. Zobrazit [Začínáme s Azure Resource Managerem](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) dokument White Paper.

## <a name="your-typical-responsibilities"></a>Typické odpovědnosti

Vaši uživatelé chtějí používat služby. Z perspektivy hlavní role je, aby tyto služby jsou pro ně dostupné. Musíte rozhodnout, jaké služby nabízí a zpřístupněte tyto služby tak, že vytvoříte plánů, nabídek a kvót. Další informace najdete v tématu [přehled nabízených služeb ve službě Azure Stack](azure-stack-offer-services-overview.md). 

Také budete muset přidat položky do [na webu marketplace](azure-stack-marketplace.md), jako jsou Image virtuálních počítačů. Nejjednodušší způsob je [stažení položek z marketplace z Azure do služby Azure Stack](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Pokud chcete testovat plány, nabídky a služby, měli byste použít [portálu user portal](azure-stack-manage-portals.md); není na portálu správce.

Kromě poskytování služeb, je nutné provést všechny regulárních povinnosti operátoru zajistit služby Azure Stack zprovozněný. Tyto povinnosti patří:

- Přidání uživatelských účtů (pro [Azure Active Directory](azure-stack-add-new-user-aad.md) nasazení nebo pro [Active Directory Federation Services](azure-stack-add-users-adfs.md) nasazení)
- [Přiřadit role (RBAC) řízení přístupu na základě role](azure-stack-manage-permissions.md) (to se neomezuje na správce.)
- [Monitorování stavu infrastruktury](azure-stack-monitor-health.md)
- Správa [sítě](azure-stack-viewing-public-ip-address-consumption.md) a [úložiště](azure-stack-manage-storage-accounts.md) prostředky
- Nahradí chybný hardwaru, například [poškozený disk nahraďte](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Co říct uživatelům

Bude potřeba dát uživatelům vědět, jak pracovat se službami v Azure stacku, jak se připojit k prostředí a jak k odběru nabídky. Kromě jakékoli vlastní dokumentace, můžete chtít poskytnout uživatelům, můžete nasměrovat uživatele na web dokumentace ke službě Azure Stack uživatelů.

**Naučte se pracovat se službami v Azure stacku**

Zde jsou informace, které vaši uživatelé musí porozumět před využívat služby a sestavovat aplikace ve službě Azure Stack. Například jsou konkrétní požadavky na verzi prostředí PowerShell a rozhraní API. Navíc existují některé funkce rozdíly mezi služby v Azure a ekvivalentní ve službě Azure Stack. Ujistěte se, že vaši uživatelé najdete v následujících článcích:

- [Klíč aspekty: použití služeb nebo vytváření aplikací pro Azure Stack](user/azure-stack-considerations.md)
- [Důležité informace týkající se virtuálních počítačů v Azure stacku](user/azure-stack-vm-considerations.md)
- [Úložiště: rozdíly a aspekty](user/azure-stack-acs-differences.md)

Informace v těchto článcích shrnuje rozdíly mezi service v Azure a Azure Stack. Doplňuje informace, které je k dispozici pro službu Azure globální dokumentace k Azure.

**Připojení ke službě Azure Stack jako uživatel**

Ve vývojovém prostředí sady Pokud uživatel nemá přístup ke vzdálené ploše k hostiteli development kit, musíte nakonfigurovat připojení virtuální privátní sítě (VPN) předtím, než získají přístup k Azure Stack. Zobrazit [připojení k Azure Stack](azure-stack-connect-azure-stack.md). 

Vaši uživatelé budou chtít vědět jak [přístup k portálu user portal ](user/azure-stack-use-portal.md) nebo jak se připojit prostřednictvím prostředí PowerShell. V prostředí integrované systémy uživatelského portálu adresy se liší podle nasazení. Budete muset uživatelům poskytnout správnou adresu URL.

Pokud používáte PowerShell, uživatelé nemusí registrovat poskytovatele prostředků před použitím služby. (Poskytovatel prostředků spravuje služba. For example, poskytovateli síťových prostředků spravuje prostředky, jako jsou virtuální sítě, síťová rozhraní a nástroje pro vyrovnávání zatížení.) Musí [nainstalovat](user/azure-stack-powershell-install.md) prostředí PowerShell, [Stáhnout](user/azure-stack-powershell-download.md) dalších modulů, a [nakonfigurovat](user/azure-stack-powershell-configure-user.md) Powershellu (která zahrnuje registrace poskytovatele prostředků).

**Přihlášení k odběru nabídky**

Předtím, než uživatel mají přístup ke službám, musí [předplatit nabídky](azure-stack-subscribe-plan-provision-vm.md) , kterou jste vytvořili jako operátor.

## <a name="where-to-get-support"></a>Kde lze získat podporu

### <a name="integrated-systems"></a>Integrované systémy

Pro integrovaný systém je koordinovanou eskalaci a řešení procesu mezi Microsoftem a našimi hardwarovými partnery výrobce OEM (OEM).

Pokud nastane problém s cloud services, podpora je dostupná prostřednictvím Microsoft podporu služby zákazníkům (CSS). Pokud klikněte na ikonu nápovědy a podpory (otazník) v pravém horním rohu portálu správce a potom klikněte na tlačítko **nová žádost o podporu**, otevře se web, kde můžete otevřít přímo prostřednictvím žádosti o podporu.

Pokud se vyskytl problém s nasazením, opravy a aktualizace hardwaru (včetně pole replaceable jednotek) a veškerý software pod značkou jiných hardwaru, jako je například software spuštěný na hostiteli životního cyklu hardwaru, obraťte se na vašeho OEM dodavatele hardwaru nejprve.

Na něco jiného obraťte se na Microsoft CSS.

### <a name="development-kit"></a>Vývojová sada

Pro development kit, můžete klást otázky týkající se podpory v [fórech Microsoftu](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Pokud klikněte na ikonu nápovědy a podpory (otazník) v pravém horním rohu portálu správce a potom klikněte na tlačítko **nová žádost o podporu**, přímo otevře web fór. Tato fóra jsou pravidelně monitorované. Vývojová sada je prostředí pro testování, neexistuje žádné oficiální podpora dostupná prostřednictvím Microsoft CSS.

## <a name="next-steps"></a>Další postup

- [Správa oblastí ve službě Azure Stack](azure-stack-region-management.md)



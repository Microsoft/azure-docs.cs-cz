---
title: Generování sestav dat využití zásobník Azure do Azure | Microsoft Docs
description: Zjistěte, jak nastavit vytváření sestav v Azure zásobníku data o využití.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 602cd6c3b2be8881bebbcebe30ec2520358b731f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Generování sestav Azure zásobníku využití dat do Azure 

Data o využití, označované taky jako datům o spotřebě, představuje objem prostředků používá. 

Azure systémy s více uzly zásobníku, které používají model fakturace na základě spotřeby by měl sestavy data o využití do Azure pro účely fakturace.  Operátory Azure zásobníku měli nakonfigurovat jejich zásobník Azure instance sestavy využití dat do Azure.

> [!NOTE]
> Generování sestav dat využití je potřeba pro uživatele Azure zásobníku více uzly, kteří licencují v rámci modelu platím jako--používání. Zadání je volitelné pro zákazníky, kteří licencí v rámci modelu kapacity (najdete v článku [jak koupit](https://azure.microsoft.com/overview/azure-stack/how-to-buy/). Uživatelé Development Kit zásobník Azure můžete Azure zásobníku operátory sestavy data o využití a otestovat funkci. Uživatelé ale neplatí žádné využití, kterým jsou vystaveni. 


![fakturace toku](media/azure-stack-usage-reporting/billing-flow.png)

Data o využití se odesílá z zásobník Azure do Azure prostřednictvím mostu Azure. V Azure systém a obchodu Spojených států zpracovává dat o využití a generuje kusovníku. Po vygenerování kusovníku vlastník předplatného Azure můžete zobrazit a stažení z [centra účtů Azure](https://account.windowsazure.com/Subscriptions). Další informace o tom, jak je licencován zásobník Azure, najdete [zásobník Azure balení a ceny dokumentu](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409).

## <a name="set-up-usage-data-reporting"></a>Nastavení sestav využití dat

Abyste mohli nastavit generování sestav dat využití, musíte [registraci vaší instanci Azure zásobník Azure](azure-stack-register.md). Jako součást procesu registrace je součást Azure most zásobníku Azure, který připojí zásobník Azure do Azure a odesílá data o využití, nakonfigurovaná. Následující data o využití se odesílá z zásobník Azure do Azure:

- **Měřicí ID** – jedinečné ID pro prostředek, která se spotřebovala.
- **Množství** – množství využití prostředků.
- **Umístění** – umístění, kde je nasazená aktuálního prostředku Azure zásobníku.
- **Identifikátor URI** – plně kvalifikovaný identifikátor URI prostředku, pro kterou je hlášena využití.
- **ID předplatného** – ID předplatného Azure zásobník uživatele. Toto je místní předplatné (Azure Stack).
- **Čas** – počáteční a koncový čas dat o využití. Není některé zpoždění mezi tím, kdy, když jsou tyto prostředky spotřebované v zásobníku Azure a při hlášení dat o využití do obchodu. Generování sestav data o využití do kanálu a obchodu Spojených států v Azure a Azure zásobníku agreguje data o využívání každých 24 hodin trvá jiné několik hodin. Ano použití, který se nachází krátce před půlnoc může zobrazí v Azure následující den.

## <a name="generate-usage-data-reporting"></a>Generování sestav dat využití

1. K testování generování sestav dat využití, vytvořte několik prostředky v Azure zásobníku. Například můžete vytvořit [účet úložiště](azure-stack-provision-storage-account.md), [virtuálního počítače Windows serveru](azure-stack-provision-vm.md) a virtuální počítač s Linuxem se základní a standardní SKU zobrazíte způsob hlášení základní použití. Data o využití pro různé typy prostředků jsou v části různé měřidla.

2. Ponechte vaše prostředky systémem pro několik hodin. Informace o využití se shromažďují přibližně jednou za hodinu. Po shromáždění, je tato data přenesou do Azure a zpracování do systému Azure commerce. Tento proces může trvat až několik hodin.

## <a name="view-usage---csp-subscriptions"></a>Zobrazení využití - odběry zprostředkovatele kryptografických služeb

Pokud jste zaregistrovali do sady Azure pomocí předplatného poskytovatele CSP, můžete zobrazit využití a poplatky stejným způsobem, na které můžete zobrazit využití platformy Azure. Použití Azure zásobníku budou zahrnuty v faktury a soubor odsouhlasení, k dispozici prostřednictvím [Partnerské centrum](https://partnercenter.microsoft.com/partner/home). Soubor odsouhlasení se aktualizuje jednou měsíčně. Pokud potřebujete pro přístup k nejnovější informace o využití zásobník Azure, můžete použít rozhraní API Center partnera.

   ![Partnerské centrum](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>Zobrazení využití – odběry smlouva Enterprise Agreement

Pokud jste zaregistrovali do sady Azure pomocí předplatné smlouvy Enterprise, můžete zobrazit využití a poplatky v [EA portál](https://ea.azure.com/). Použití Azure zásobníku bude součástí pokročilé stahování společně s Azure použití části sestavy na portálu EA. 

## <a name="view-usage--other-subscriptions"></a>Zobrazení využití – odběr

Pokud jste zaregistrovali do sady Azure pomocí jakékoli jiné předplatné typu, například platím průběžně předplatného, můžete zobrazit v Centru pro účet Azure využití a poplatky. Přihlaste se k [centra účtů Azure](https://account.windowsazure.com/Subscriptions) jako Azure účtu správce a vyberte předplatné Azure, který jste použili k registraci v Azure zásobníku. Můžete zobrazit zásobník Azure data o využití z množství účtovat poplatek za každou využité prostředky, jak je znázorněno na následujícím obrázku:

   ![fakturace toku](media/azure-stack-usage-reporting/pricing-details.png)

Pro Azure Development Kit zásobníku nejsou účtovat prostředky Azure zásobníku, ceny se zobrazí jako 0,00 Kč. Když je obecně dostupná několika uzly zásobník Azure, uvidíte skutečné náklady pro každý z těchto prostředků.

## <a name="which-azure-stack-deployments-are-charged"></a>Budou se účtovat které nasazení zásobník Azure?

Využití prostředků je zdarma pro Azure zásobníku Development Kit. Zatímco Azure zásobníku systémech s více uzly, budou účtovat virtuálních počítačů pracovního vytížení, služby úložiště a aplikační služby.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Účtujeme uživatelů pro infrastrukturu virtuálních počítačů?

Ne. Data o využití pro některé poskytovatel prostředků Azure zásobníku virtuálních počítačů je hlášena do Azure, ale neexistují žádné poplatky pro tyto virtuální počítače ani pro virtuální počítače vytvořené během nasazení povolit infrastruktury Azure zásobníku.  

Uživatelům se účtují poplatky pro virtuální počítače, které běží pod odběry klienta. Všechny úlohy musí být nasazený v části předplatná klienta pro dosažení souladu s licenční podmínky Azure zásobníku.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Mám licenci systému Windows Server, který chcete použít v zásobníku Azure, jak se provádí?

Použití existující licence zabraňuje generování využití měřidla. Licence pro existující Server systému Windows lze použít v zásobníku Azure, jak je popsáno v části "Použití existující software s Azure zásobníku" [průvodci licencování Azure zásobníku](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409). Zákazníci potřebujete nasadit své virtuální počítače Windows serveru, jak je popsáno v [hybridní výhody pro Windows Server licenci](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) tématu, aby bylo možné používat své existující licence.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Jaké předplatné je účtovat poplatek za prostředky spotřebované?
Odběr, který je k dispozici při [registrace zásobník Azure s Azure](azure-stack-register.md) je účtován.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Jaké typy odběry jsou podporovány pro generování sestav využití dat?

Předplatné Enterprise Agreement (EA) a zprostředkovatele kryptografických služeb multinode zásobník Azure, jsou podporovány. Pro Azure Development Kit zásobníku Enterprise Agreement (EA), průběžné platby, CSP a MSDN odběry podporují, generování sestav dat využití.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Podporuje generování sestav práci v cloudech svrchovaných využití dat?

V sadě Azure zásobníku Development Kit generování sestav dat využití vyžaduje předplatné, které jsou vytvořeny v globálním systému Azure. Odběry vytvořit v jednom z svrchovaných cloudy (cloudy Azure Government, Azure v Německu a Azure China) nelze zaregistrovat s Azure, takže nepodporují generování sestav dat využití.

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>Jak poznáte uživatelé zásobník Azure data o využití na portálu Azure fakturace?

Uživatelé mohou vidět data o využití Azure zásobníku v souboru podrobnosti o využití. Potřebujete vědět o tom, jak získat soubor podrobnosti o využití, naleznete [stáhnout soubor využití z centra účtů Azure článku](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). Podrobnosti o využití souboru obsahuje měřidla zásobník Azure, které identifikují Azure zásobníku úložiště a virtuálních počítačů. Všechny prostředky používané v zásobníku Azure jsou v části oblasti s názvem "Azure zásobníku."

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Proč se využití ohlášena v zásobníku Azure neshoduje se sestava vygenerována z centra účtů Azure?

Je vždy delaybetween, dat o využití hlášené data o využití hlášené centra účtů Azure a použití Azure zásobníku rozhraní API... Toto opoždění je čas potřebný k odeslat data o využití ze zásobníku Azure do Azure commerce. Z důvodu tato prodleva využití, který se nachází krátce před půlnoc může zobrazí v Azure následující den. Pokud použijete [rozhraní API Správce Azure zásobníku využití](azure-stack-provider-resource-api.md)a porovnejte výsledky využití v portálu Azure fakturace, uvidíte rozdíl.

## <a name="next-steps"></a>Další postup

* [Rozhraní API využití zprostředkovatele](azure-stack-provider-resource-api.md)  
* [Rozhraní API využití tenanta](azure-stack-tenant-resource-usage-api.md)
* [Nejčastější dotazy k využití](azure-stack-usage-related-faq.md)
* [Spravovat využití a fakturace jako poskytovatele cloudových služeb](azure-stack-add-manage-billing-as-a-csp.md)

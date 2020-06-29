---
title: Nasazení poskytovatele partnera zabezpečení Azure Firewall Manageru
description: Naučte se nasadit zprostředkovatele zabezpečení Azure Firewall Manageru pomocí Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: victorh
ms.openlocfilehash: 91cf453247bfe4fa689df34bdf6b585ac72686aa
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2020
ms.locfileid: "85509050"
---
# <a name="deploy-a-security-partner-provider-preview"></a>Nasazení poskytovatele partnera zabezpečení (Preview)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

*Poskytovatelé zabezpečení* v nástroji Azure firewall Manager vám umožňují používat vaše známé nabídky zabezpečení jako služby (SECaaS) od společnosti Microsoft k ochraně internetového přístupu pro vaše uživatele.

Další informace o podporovaných scénářích a návodech k osvědčeným postupům najdete v tématu [co jsou důvěryhodné bezpečnostní partneři (Preview)?](trusted-security-partners.md).

Podporovaní partneři zabezpečení jsou **ZScaler**, **Check Point**a **iboss** pro tuto verzi Preview. Podporované oblasti jsou WestCentralUS, NorthCentralUS, WestUS, WestUS2 a EastUS.

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Verze Preview Azure Firewall Manageru musí být explicitně povolená pomocí `Register-AzProviderFeature` příkazu PowerShellu.

Z příkazového řádku PowerShellu spusťte následující příkazy:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Dokončení registrace funkce trvá až 30 minut. Spusťte následující příkaz, který zkontroluje stav registrace:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Nasazení poskytovatele zabezpečení třetí strany v novém centru

Tuto část můžete přeskočit, pokud nasazujete poskytovatele třetí strany do existujícího centra.

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
2. Do pole **Hledat**zadejte **Správce brány firewall** a vyberte ho v části **služby**.
3. Přejděte na **Začínáme**. Vyberte **vytvořit zabezpečené virtuální rozbočovač**. 
4. Zadejte předplatné a skupinu prostředků, vyberte podporovanou oblast a přidejte informace o rozbočovači a virtuální síti WAN. 
5. **Nasazení služby VPN Gateway** je ve výchozím nastavení povolené. K nasazení důvěryhodného partnera zabezpečení v centru se vyžaduje VPN Gateway. 
6. Vyberte **Další: Azure firewall**
   > [!NOTE]
   > Důvěryhodní bezpečnostní partneři se připojují k vašemu rozbočovači pomocí VPN Gateway tunely. Pokud VPN Gateway odstraníte, ztratí se připojení k vašim důvěryhodným partnerům zabezpečení.
7. Pokud chcete nasadit Azure Firewall pro filtrování privátních přenosů spolu s poskytovatelem služeb třetích stran pro filtrování internetového provozu, vyberte zásadu pro Azure Firewall. Podívejte se na [podporované scénáře](trusted-security-partners.md#key-scenarios).
8. Pokud chcete nasadit pouze poskytovatele zabezpečení třetí strany v centru, vyberte **Azure firewall: povoleno/zakázáno** a nastavte jej na **zakázáno**. 
9. Vyberte **Další: poskytovatel zabezpečení partnera**.
10. Vyberte **poskytovatele partnera zabezpečení** a nastavte jej na **povoleno**. Vyberte partnera. 
11. Vyberte **Další**. 
12. Zkontrolujte obsah a pak vyberte **vytvořit**.

Nasazení brány VPN může trvat déle než 30 minut.

Pokud chcete ověřit, že se vytvořilo centrum, přejděte na >zabezpečená centra Azure Firewall Manager. Pokud chcete zobrazit název partnera a stav, ve kterém se **nečeká připojení zabezpečení**, vyberte stránku Přehled >hub.

Jakmile se vytvoří centrum a nastaví se partner zabezpečení, pokračujte tím, že připojíte poskytovatele zabezpečení k centru.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Nasazení poskytovatele zabezpečení třetí strany v existujícím centru

Můžete také vybrat existující centrum ve virtuální síti WAN a převést ho na *zabezpečené virtuální rozbočovač*.

1. V **Začínáme**vyberte **převést existující centra**.
2. Vyberte předplatné a existující centrum. Postupujte podle zbývajících kroků a nasaďte poskytovatele třetí strany v novém centru.

Pamatujte, že je potřeba nasadit bránu VPN, aby se převedlo existující centrum na zabezpečené centrum s poskytovateli třetích stran.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Konfigurace poskytovatelů zabezpečení jiných výrobců pro připojení k zabezpečenému centru

Pokud chcete nastavit tunely na VPN Gateway svého virtuálního rozbočovače, poskytovatelé třetích stran potřebují přístupová práva k vašemu centru. Provedete to tak, že přidružíte instanční objekt k vašemu předplatnému nebo skupině prostředků a udělíte přístupová práva. Tyto přihlašovací údaje pak musíte poskytnout třetí straně pomocí svého portálu.

### <a name="create-and-authorize-a-service-principal"></a>Vytvoření a autorizace instančního objektu

1. Vytvořit objekt služby Azure Active Directory (AD): adresu URL pro přesměrování můžete přeskočit. 

   [Postup: Vytvoření aplikace Azure AD a instančního objektu s přístupem k prostředkům pomocí portálu](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. Přidejte přístupová práva a rozsah objektu služby.
   [Postup: Vytvoření aplikace Azure AD a instančního objektu s přístupem k prostředkům pomocí portálu](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > Přístup můžete omezit jenom na vaši skupinu prostředků, abyste měli podrobnější kontrolu.

### <a name="visit-partner-portal"></a>Navštívit portál pro partnery

1. Dokončete nastavení podle pokynů uvedených v partnerovi. Patří sem odeslání informací AAD pro detekci a připojení k centru, aktualizace zásad odchozího přenosu a kontrola stavu připojení a protokolů.

   - [Zscaler: nakonfigurujte Microsoft Azure integraci virtuální sítě WAN](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration).
   - [Check Point: konfigurace Microsoft Azure Integration Virtual WAN](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm)
   
2. Na portálu Azure Virtual WAN v Azure se můžete podívat na stav vytvoření tunelu. Jakmile se tunely **připojí** na Azure i na portálu pro partnery, pokračujte dalšími kroky nastavení tras pro výběr směrování a virtuální sítě by měly odesílat internetové přenosy partnerovi.

## <a name="configure-route-settings"></a>Konfigurovat nastavení tras

1. Přejděte do okna správce Azure Firewall – > zabezpečená centra. 
2. Vyberte centrum. Stav centra by teď měl místo **nedokončeného připojení zabezpečení**zobrazovat **zřízené** .

   Ujistěte se, že se poskytovatel třetí strany může připojit k centru. Tunely v bráně VPN by měly být v **připojeném** stavu. Tento stav je více reflektující se na stav připojení mezi centrem a partnerem třetí strany, a to v porovnání s předchozím stavem.
3. Vyberte centrum a přejděte na **nastavení směrování**.

   Když nasadíte poskytovatele třetí strany do centra, převede centrum na *zabezpečené virtuální rozbočovač*. Tím se zajistí, že poskytovatel třetí strany inzeruje trasu s hodnotou 0.0.0.0/0 (výchozí) trasy k centru. Připojení virtuální sítě a lokality připojené k centru ale tuto trasu nezískají, pokud se nerozhodnete, která připojení by měla získat tuto výchozí trasu.
4. V části **internetový provoz**vyberte možnost **VNet-to-Internet** nebo připojení z **pobočky k Internetu** , aby byly trasy nakonfigurované prostřednictvím třetí strany.

   Tato akce určuje, který typ provozu má být směrován do centra, ale nemá vliv na trasy v virtuální sítě nebo větvích. Tyto trasy nejsou šířené do všech virtuální sítě/větví připojených k centru ve výchozím nastavení.
5. Musíte vybrat **zabezpečená připojení** a vybrat připojení, na kterých mají být tyto trasy nastaveny. Označuje, které virtuální sítě/větve můžou zahájit odesílání internetového provozu poskytovateli třetí strany.
6. V **nastavení směrování**vyberte **zabezpečená připojení** v části internetový provoz a pak vyberte virtuální síť nebo větve (*lokality* ve virtuální síti WAN), které chcete zabezpečit. Vyberte **zabezpečený internetový provoz**.
   ![Zabezpečení internetového provozu](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Přejděte zpět na stránku centra. Stav **důvěryhodného partnera zabezpečení** centra by měl být teď **zabezpečený**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Síťový provoz ve větvi nebo virtuální síti prostřednictvím služby třetí strany

V dalším kroku můžete zkontrolovat, jestli virtuální počítače virtuální sítě nebo pobočky mají přístup k Internetu, a ověřit, že provoz probíhá do služby třetí strany.

Po dokončení postupu nastavení směrování budou virtuální počítače virtuální sítě a větvení lokality 0/0 odesílány do trasy služby třetí strany. Do těchto virtuálních počítačů nemůžete protokol RDP nebo SSH. Pokud se chcete přihlásit, můžete nasadit službu [Azure bastionu](../bastion/bastion-overview.md) ve virtuální síti s partnerským vztahem.

## <a name="next-steps"></a>Další kroky

- [Kurz: zabezpečení cloudové sítě pomocí nástroje Azure Firewall Manager ve verzi Preview pomocí Azure Portal](secure-cloud-network.md)
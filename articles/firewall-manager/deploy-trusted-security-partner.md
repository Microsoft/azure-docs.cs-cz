---
title: Nasazení důvěryhodného partnera zabezpečení Azure Firewall Manageru
description: Naučte se nasadit důvěryhodné zabezpečení Azure Firewall Manageru pomocí Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "73931302"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Nasazení důvěryhodného partnera pro zabezpečení (Preview)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

*Důvěryhodní partneři zabezpečení* v Azure firewall Manageru vám umožní používat vaše známé nabídky zabezpečení jako služby (SECaaS) od jiných výrobců k ochraně internetového přístupu pro vaše uživatele.

Další informace o podporovaných scénářích a návodech k osvědčeným postupům najdete v tématu [co jsou důvěryhodné bezpečnostní partneři (Preview)?](trusted-security-partners.md).

Podporovaní partneři zabezpečení jsou **ZScaler** a **iboss** pro tuto verzi Preview. Podporované oblasti jsou WestCentralUS, NorthCentralUS, WestUS, WestUS2 a EastUS.

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Verze Preview Azure Firewall Manageru musí být explicitně povolená pomocí příkazu `Register-AzProviderFeature` PowerShellu.

Z příkazového řádku PowerShellu spusťte následující příkazy:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Dokončení registrace funkce trvá až 30 minut. Spusťte následující příkaz, který zkontroluje stav registrace:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Nasazení poskytovatele zabezpečení třetí strany v novém centru

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
9. Vyberte **Další: důvěryhodní partneři zabezpečení**.
10. Vyberte **důvěryhodný partner zabezpečení** a nastavte jej na **povoleno**. Vyberte partnera. 
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

1. Vytvořit objekt služby Azure Active Directory (AD): adresu URL pro přesměrování můžete přeskočit. 

   [Postupy: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Přidejte přístupová práva a rozsah objektu služby.
   [Postupy: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > Přístup můžete omezit jenom na vaši skupinu prostředků, abyste měli podrobnější kontrolu.
3. Postupujte podle pokynů [ZScaler: konfigurace Microsoft Azure instrukcí pro integraci virtuální sítě WAN](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) na:

   - Přihlaste se na portál pro partnery a přidejte svoje přihlašovací údaje, abyste měli důvěryhodného partnera přístup k zabezpečenému centru.
   - Synchronizujte virtuální rozbočovače na portálu pro partnery a nastavte tunelové propojení na virtuální rozbočovač. Můžete to udělat až po ověření přihlašovacích údajů pro ověřování Azure AD.
   
4. Na portálu Azure Virtual WAN v Azure se můžete podívat na stav vytvoření tunelu. Jakmile se tunely **připojí** na Azure i na portálu pro partnery, pokračujte dalšími kroky nastavení tras pro výběr směrování a virtuální sítě by měly odesílat internetové přenosy partnerovi.

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





---
title: Nasazení důvěryhodného partnera správce Azure Firewall
description: Přečtěte si, jak nasadit důvěryhodné zabezpečení Azure Firewall Manageru pomocí portálu Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931302"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Nasazení důvěryhodného partnera pro zabezpečení (Preview)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

*Důvěryhodní partneři zabezpečení* ve Správci Azure Firewall vám pomohou používat známé nabídky zabezpečení jako služby (SECaaS) třetích stran k ochraně přístupu k Internetu pro vaše uživatele.

Další informace o podporovaných scénářích a pokynech pro osvědčené postupy naleznete v tématu [Co jsou důvěryhodní partneři zabezpečení (preview)?](trusted-security-partners.md).

Podporovaní partneři zabezpečení jsou **ZScaler** a **iboss** pro tento náhled. Podporované oblasti jsou WestCentralUS, NorthCentralUS, WestUS, WestUS2 a EastUS.

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Azure Firewall Manager Preview musí `Register-AzProviderFeature` být explicitně povolený pomocí příkazu PowerShell.

Z příkazového řádku Prostředí PowerShell spusťte následující příkazy:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Dokončení registrace funkce trvá až 30 minut. Chcete-li zkontrolovat stav registrace, spusťte následující příkaz:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Nasazení poskytovatele zabezpečení jiného výrobce v novém rozbočovači

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
2. Do **pole Hledat**zadejte příkaz Správce brány **firewall** a vyberte jej v části **Služby**.
3. Přejděte na **Začínáme**. Vyberte **možnost Vytvořit zabezpečené virtuální centrum**. 
4. Zadejte odběr a skupinu prostředků, vyberte podporovanou oblast a přidejte informace o rozbočovači a virtuální paměti WAN. 
5. **Nasazení brány VPN** je ve výchozím nastavení povoleno. K nasazení důvěryhodného partnera zabezpečení v centru je vyžadována brána VPN. 
6. Vyberte **další: Brána Azure Firewall**
   > [!NOTE]
   > Důvěryhodní partneři zabezpečení se připojují k vašemu rozbočovači pomocí tunelových propojení brány VPN. Pokud odstraníte bránu VPN, dojde ke ztrátě připojení k vašim důvěryhodným partnerům zabezpečení.
7. Pokud chcete nasadit Azure Firewall pro filtrování privátního provozu spolu s poskytovatelem služeb třetí strany pro filtrování internetového provozu, vyberte zásadu pro Azure Firewall. Podívejte se na [podporované scénáře](trusted-security-partners.md#key-scenarios).
8. Pokud chcete v centru nasadit jenom poskytovatele zabezpečení jiného výrobce, vyberte **Azure Firewall: Povoleno/Zakázáno** a nastavte ho na **Zakázanou**. 
9. Vyberte **další: Důvěryhodní partneři zabezpečení**.
10. Výběrem **možnosti Důvěryhodného partnera zabezpečení** nastavte **položku Povoleno**. Vyberte partnera. 
11. Vyberte **další**. 
12. Zkontrolujte obsah a pak vyberte **Vytvořit**.

Nasazení brány VPN může trvat déle než 30 minut.

Chcete-li ověřit, že centrum bylo vytvořeno, přejděte na Azure Firewall Manager->zabezpečených rozbočovačů. Vyberte stránku přehled >rozbočovače, chcete-li zobrazit název partnera a stav čekající na **připojení zabezpečení**.

Po vytvoření rozbočovače a nastavení partnera zabezpečení pokračujte v připojení poskytovatele zabezpečení k rozbočovači.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Nasazení poskytovatele zabezpečení jiného výrobce v existujícím rozbočovači

Můžete také vybrat existující rozbočovač ve virtuální matné wan a převést na *zabezpečené virtuální rozbočovač*.

1. Ve **zchálení**vyberte **Převést existující rozbočovače**.
2. Vyberte předplatné a existující centrum. Postupujte podle zbývajících kroků k nasazení poskytovatele třetí strany v novém centru.

Nezapomeňte, že brána VPN musí být nasazena, aby bylo možné převést existující rozbočovač na zabezpečené centrum s poskytovateli třetích stran.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Konfigurace poskytovatelů zabezpečení jiných výrobců pro připojení k zabezpečenému rozbočovači

K nastavení tunelů do brány VPN virtuálního centra potřebují poskytovatelé třetích stran přístupová práva k vašemu rozbočovači. Chcete-li to provést, přidružte instanční objekt k předplatnému nebo skupině prostředků a udělte přístupová práva. Potom musíte poskytnout tato pověření třetí straně pomocí jejich portálu.

1. Vytvoření instancí služby Azure Active Directory (AD): Adresu URL přesměrování můžete přeskočit. 

   [Postup: Použití portálu k vytvoření aplikace azure a instančního objektu Azure AD, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Přidejte přístupová práva a obor pro instanční objekt.
   [Postup: Použití portálu k vytvoření aplikace azure a instančního objektu Azure AD, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > Přístup můžete omezit pouze na skupinu prostředků pro podrobnější ovládací prvek.
3. Postupujte podle [zscaleru: Konfigurace](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) pokynů pro integraci virtuální sítě WAN microsoft azure:

   - Přihlaste se k partnerskému portálu a přidejte svá pověření, abyste důvěryhodnému partnerovi poskytli přístup k zabezpečenému centru.
   - Synchronizujte virtuální rozbočovače na partnerském portálu a nastavte tunelové propojení do virtuálního rozbočovače. Můžete tak učinit po ověření přihlašovacích údajů Azure AD.
   
4. Můžete se podívat na stav vytvoření tunelového propojení na portálu Azure Virtual WAN v Azure. Jakmile se tunely zobrazí **připojené** na portálu Azure i na portálu pro partnery, pokračujte dalšími kroky k nastavení tras pro výběr větví a virtuálních sítí, které by měly odesílat internetový provoz partnerovi.

## <a name="configure-route-settings"></a>Konfigurace nastavení trasy

1. Přejděte na zabezpečená centra Azure Firewall Manager ->. 
2. Vyberte rozbočovač. Stav rozbočovače by nyní měl zobrazovat **zřízené** místo **čekající na zabezpečení připojení**.

   Ujistěte se, že poskytovatel třetí strany se může připojit k rozbočovači. Tunely na bráně VPN by měly být ve **stavu Připojení.** Tento stav je více odráží stav připojení mezi rozbočovačem a partnerem třetí strany, ve srovnání s předchozím stavem.
3. Vyberte rozbočovač a přejděte do **nastavení trasy**.

   Když nasadíte zprostředkovatele třetí strany do rozbočovače, převede rozbočovač na *zabezpečené virtuální rozbočovač*. Tím zajistíte, že poskytovatel třetí strany inzeruje trasu 0.0.0.0/0 (výchozí) do centra. Připojení virtuální sítě a lokality připojené k rozbočovači však tuto trasu nezískají, pokud se nepřihlásíte, která připojení by měla získat tuto výchozí trasu.
4. V části **Internetový provoz**vyberte **možnost VNet-to-Internet** nebo **Branch-to-Internet** nebo obojí, aby byly trasy nakonfigurovány jako odesílané prostřednictvím třetí strany.

   To pouze označuje, jaký typ provozu by měl být směrován do rozbočovače, ale zatím nemá vliv na trasy ve virtuálních sítích nebo větvích. Tyto trasy nejsou ve výchozím nastavení rozšířeny do všech virtuálních sítí nebo větví připojených k rozbočovači.
5. Musíte vybrat **zabezpečená připojení** a vybrat připojení, na kterých mají být tyto trasy nastaveny. To znamená, že virtuální sítě nebo větve můžete začít odesílat internetový provoz na poskytovatele třetí strany.
6. V **nastavení trasy**vyberte **Zabezpečená připojení** v části Internetový provoz a pak vyberte virtuální síť nebo pobočky *(lokality* ve virtuální síti WAN), které chcete zabezpečit. Vyberte **možnost Zabezpečený internetový provoz**.
   ![Bezpečný internetový provoz](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Přejděte zpět na stránku rozbočovačů. Stav **důvěryhodného partnera pro zabezpečení** centra by nyní měl být **zabezpečen**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Pobočka nebo internetový provoz virtuální sítě prostřednictvím služby jiného výrobce

Dále můžete zkontrolovat, jestli virtuální počítače virtuální sítě nebo pobočka sítě přístup k Internetu a ověřit, že provoz proudí do služby jiného výrobce.

Po dokončení kroky nastavení trasy virtuálních počítačů, stejně jako pobočky lokality jsou odesílány 0/0 na trasu služby třetí strany. Nelze RDP nebo SSH do těchto virtuálních počítačů. Pokud se chcete přihlásit, můžete nasadit službu [Azure Bastion](../bastion/bastion-overview.md) v partnerské virtuální síti.

## <a name="next-steps"></a>Další kroky

- [Kurz: Zabezpečení cloudové sítě pomocí Azure Firewall Manager Preview pomocí portálu Azure](secure-cloud-network.md)





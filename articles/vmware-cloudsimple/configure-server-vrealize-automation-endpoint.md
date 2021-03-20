---
title: Řešení Azure VMware podle CloudSimple – nastavení vCenter v privátním cloudu pro automatizaci vRealize
description: Popisuje, jak nastavit server VMware vCenter v privátním cloudu CloudSimple jako koncový bod pro automatizaci VMware vRealize.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b6c6a320e6299808a91214476c8c0460f9f53d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895049"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>Nastavení vCenter v privátním cloudu pro automatizaci VMware vRealize

VMware vCenter Server můžete nastavit na privátním cloudu CloudSimple jako koncový bod pro automatizaci VMware vRealize.

## <a name="before-you-begin"></a>Než začnete

Před konfigurací serveru vCenter proveďte tyto úlohy:

* Nakonfigurujte [připojení VPN typu Site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) mezi místním prostředím a Vaším privátním cloudem.
* [Nakonfigurujte předávání DNS místních požadavků DNS](on-premises-dns-setup.md) na servery DNS vašeho privátního cloudu.
* Odešlete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a vytvořte uživatele s právy pro správu VRealize Automation IaaS sadou oprávnění, která jsou uvedena v následující tabulce.

| Hodnota atributu | Oprávnění |
------------ | ------------- |  
| Úložiště dat |  Přidělit místo <br> Procházet úložiště dat |
| Cluster úložiště dat | Konfigurace clusteru úložiště dat |
| Složka | Vytvořit složku <br>Odstranit složku |
| Globální |  Správa vlastních atributů<br>Nastavení vlastního atributu |
| Síť | Přiřadit síť |
| Oprávnění | Upravit oprávnění |
| Prostředek | Přiřazení virtuálního počítače ke fondu zdrojů<br>Migrace vypnutého virtuálního počítače<br>Migrace zapnutá na virtuálním počítači |
| Inventář virtuálních počítačů |  Vytvořit z existujících<br>Vytvořit nové<br>Přesunout<br>Odebrat | 
| Interakce virtuálního počítače |  Konfigurace média CD<br>Interakce konzoly<br>Připojení zařízení<br>Vypnutí napájení<br>Zapnout<br>Resetovat<br>Suspend<br>Instalace nástrojů | 
| Konfigurace virtuálního počítače |  Přidat existující disk<br>Přidat nový disk<br>Přidat nebo odebrat<br>Odebrat disk<br>Pokročilý<br>Změna počtu PROCESORů<br>Změnit prostředek<br>Zvětšit virtuální disk<br>Change Tracking disku<br>Memory (Paměť)<br>Úprava nastavení zařízení<br>přejmenování<br>Nastavit poznámku (verze 5,0 a novější)<br>Nastavení<br>Umístění swapfile |
| Zřizování |  Přizpůsobení<br>Klonovat šablonu<br>Klonovat virtuální počítač<br>Nasazení šablony<br>Číst specifikace přizpůsobení |
| Stav virtuálního počítače | Vytvořit snímek<br>Odebrat snímek<br>Vrátit se ke snímku |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Instalace automatizace vRealize do místního prostředí

1. Přihlaste se k zařízení vRealize Automation IaaS Server jako správce IaaS, kterého vám podpora CloudSimple vytvořila.
2. Nasaďte agenta vSphere pro koncový bod služby vRealize Automation.
    1. Přejděte na adresu https://*vra-URL*: 5480/instalační program, kde *vra-URL* je adresa URL, kterou používáte pro přístup k uživatelskému rozhraní správy automatizace vRealize.
    2. Kliknutím na **instalační program IaaS** Stáhněte instalační program.<br>
    Konvence pojmenování pro instalační soubor je setup_ *vra-URL* @5480.exe .
    3. Spusťte instalační program. Na obrazovce Vítejte klikněte na **Další**.
    4. Přijměte smlouvu EULA a klikněte na tlačítko **Další**.
    5. Zadejte přihlašovací údaje, klikněte na **přijmout certifikát** a pak klikněte na **Další**.
    ![přihlašovací údaje vRA](media/configure-vra-endpoint-login.png)
    6. Vyberte **vlastní instalace** a **proxy agenti** a klikněte na **Další**.
    ![typ instalace vRA](media/configure-vra-endpoint-install-type.png)
    7. Zadejte přihlašovací údaje serveru IaaS a klikněte na **Další**. Pokud používáte službu Active Directory, zadejte uživatelské jméno ve formátu **doména \ uživatel** . V opačném případě použijte **user@domain** formát.
    ![informace o přihlášení vRA](media/configure-vra-endpoint-account.png)
    8. Jako nastavení proxy zadejte **vSphere** pro **typ agenta**. Zadejte název agenta.
    9. Do polí hostitel **služby správce** a **hostitele webové služby správce modelů** zadejte plně kvalifikovaný název domény IaaS serveru. Kliknutím na **test** otestujte připojení pro každou z hodnot plně kvalifikovaného názvu domény. Pokud se test nezdařil, upravte nastavení DNS tak, aby byl název hostitele serveru IaaS vyřešený.
    10. Zadejte název koncového bodu vCenter serveru pro privátní cloud. Poznamenejte si název, který chcete použít později v procesu konfigurace.

        ![Instalační proxy server vRA](media/configure-vra-endpoint-proxy.png)

    11. Klikněte na **Next** (Další).
    12. Klikněte na **Install** (Nainstalovat).

## <a name="configure-the-vsphere-agent"></a>Konfigurace agenta vSphere

1. Přejít na https://*vra-URL*/vcac a přihlaste se jako **ConfigurationAdmin**.
2. Vyberte koncové body pro koncové body **infrastruktury**  >    >  .
3. Vyberte **Nový**  >  **Virtual**  >  **vSphere**.
4. Zadejte název koncového bodu vSphere, který jste zadali v předchozím postupu.
5. V poli **adresa** zadejte adresu URL privátního cloudu vCenter Server ve formátu https://*vCenter – FQDN*/SDK, kde *vCenter-FQDN* je název vCenter serveru.
6. Zadejte přihlašovací údaje pro uživatele s právy pro správu vRealize Automation IaaS, kterého vám CloudSimple podpora vytvořila.
7. Klikněte na **Test připojení** a ověřte přihlašovací údaje uživatele. Pokud test neproběhne úspěšně, ověřte adresu URL, informace o účtu a [název koncového bodu](#verify-the-endpoint-name) a znovu spusťte test.
8. Po úspěšném testu kliknutím na tlačítko **OK** vytvořte koncový bod vSphere.
    ![přístup ke konfiguraci koncového bodu vRA](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Ověření názvu koncového bodu

Pro identifikaci správného názvu koncového bodu vCenter serveru udělejte toto:

1. Otevřete příkazový řádek na zařízení IaaS.
2. Změňte adresář na C:\Program Files (x86) \VMware\vCAC\Agents\agent-name, kde *Agent-Name* je název, který jste přiřadili ke koncovému bodu vCenter serveru.
3. Spusťte následující příkaz.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

Výstup je podobný následujícímu. Hodnota `managementEndpointName` pole je název koncového bodu.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```

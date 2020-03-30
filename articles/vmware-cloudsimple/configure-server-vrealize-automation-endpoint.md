---
title: Řešení Azure VMware od CloudSimple – Nastavení vCenter u privátního cloudu pro automatizaci vRealize
description: Popisuje, jak nastavit server VMware vCenter na cloudu CloudSimple Private Cloud jako koncový bod pro automatizaci VMware vRealize.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df73acfc469a8b7b5329b61095aefdbd73baafd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024836"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>Nastavení vCenter v privátním cloudu pro automatizaci VMware vRealize

Server VMware vCenter můžete nastavit ve cloudu CloudSimple Private Cloud jako koncový bod pro automatizaci VMware vRealize.

## <a name="before-you-begin"></a>Než začnete

Před konfigurací serveru vCenter proveďte tyto úkoly:

* Nakonfigurujte připojení VPN mezi místním prostředím a [privátním](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) cloudem.
* [Nakonfigurujte předávání dns místních požadavků DNS](on-premises-dns-setup.md) na servery DNS pro váš privátní cloud.
* Odešlete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) k vytvoření vRealize Automation IaaS administrativní uživatel se sadou oprávnění, které jsou uvedeny v následující tabulce.

| Hodnota atributu | Oprávnění |
------------ | ------------- |  
| Úložiště dat |  Přidělení místa <br> Procházet úložiště dat |
| Cluster úložiště dat | Konfigurace clusteru úložiště dat |
| Složka | Vytvořit složku <br>Odstranit složku |
| Globální |  Spravovat vlastní atributy<br>Nastavení vlastního atributu |
| Network (Síť) | Přiřadit síť |
| Oprávnění | Změnit oprávnění |
| Prostředek | Přiřazení virtuálního virtuálního soudu fondu zdrojů<br>Migrace vypnutý virtuální počítač<br>Migrace na virtuální mno žado mě |
| Inventář virtuálních strojů |  Vytvořit z existujících<br>Vytvořit nové<br>Přesunout<br>Odebrat | 
| Interakce virtuálního počítače |  Konfigurace média CD<br>Interakce konzoly<br>Připojení zařízení<br>Vypnutí napájení<br>Zapnutí<br>Resetovat<br>Suspend<br>Instalace nástrojů | 
| Konfigurace virtuálního počítače |  Přidat existující disk<br>Přidat nový disk<br>Přidat nebo odebrat<br>Odebrat disk<br>Upřesnit<br>Změna počtu procesorů<br>Změnit zdroj<br>Rozšířit virtuální disk<br>Sledování změn disku<br>Memory (Paměť)<br>Změna nastavení zařízení<br>Přejmenovat<br>Nastavení poznámky (verze 5.0 a novější)<br>Nastavení<br>Umístění odkládacího souboru |
| Zřizování |  Přizpůsobení<br>Šablona klonování<br>Klonovat virtuální počítač<br>Nasazení šablony<br>Přečtěte si specifikace vlastního nastavení |
| Stav virtuálního počítače | Vytvořit snímek<br>Odebrat snímek<br>Vrátit se ke snímku |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Instalace vRealize Automation v místním prostředí

1. Přihlaste se k vRealize Automation IaaS server zařízení jako správce IaaS, který CloudSimple Support vytvořil pro vás.
2. Nasazení agenta vSphere pro koncový bod vRealize Automation.
    1. Přejděte na https://*vra-url*:5480/installer, kde *vra-url* je adresa URL, kterou používáte pro přístup k uživatelskému rozhraní správy vRealize Automation.
    2. Kliknutím na **Instalační program Služby IaaS** stáhněte instalační program.<br>
    Konvence pojmenování instalačního souboru je setup_*vra-url*@5480.exe.
    3. Spusťte instalační program. Na obrazovce Vítejte klikněte na **Další**.
    4. Přijměte eula a klepněte na tlačítko **Další**.
    5. Zadejte přihlašovací údaje, klepněte na tlačítko **Přijmout certifikát**a potom klepněte na tlačítko **Další**.
    ![pověření vRA](media/configure-vra-endpoint-login.png)
    6. Vyberte **Možnost Vlastní instalační** a proxy **agenti** a klepněte na tlačítko **Další**.
    ![typ instalace vRA](media/configure-vra-endpoint-install-type.png)
    7. Zadejte přihlašovací informace serveru IaaS a klepněte na tlačítko **Další**. Pokud používáte službu Active Directory, zadejte uživatelské jméno do **formátu domain\user.** V opačném **user@domain** případě použijte formát.
    ![vRA přihlašovací údaje](media/configure-vra-endpoint-account.png)
    8. Pro nastavení proxy serveru zadejte **vSphere** pro **typ agenta**. Zadejte název agenta.
    9. Zadejte fqdn serveru IaaS do polí **Správce hostitele služby** správce a hostitele **webové služby Správce modelů.** Klepnutím na tlačítko **Testovat** otestujte připojení pro každou hodnotu v yKTN. Pokud se test nezdaří, upravte nastavení DNS tak, aby byl název hostitele serveru IaaS přeložen.
    10. Zadejte název koncového bodu serveru vCenter pro privátní cloud. Zaznamenejte název pro pozdější použití v procesu konfigurace.

        ![vRA nainstalovat proxy](media/configure-vra-endpoint-proxy.png)

    11. Klikněte na **Další**.
    12. Klepněte na tlačítko **Instalovat**.

## <a name="configure-the-vsphere-agent"></a>Konfigurace agenta vSphere

1. Přejděte na https://*vra-url*/vcac a přihlaste se jako **ConfigurationAdmin**.
2. Vyberte koncové**body koncových bodů** >  **infrastruktury** > .**Endpoints**
3. Vyberte **nový** > **virtuální** > **vSphere**.
4. Zadejte název koncového bodu vSphere, který jste zadali v předchozím postupu.
5. Do **pole Adresa**zadejte adresu URL serveru vCentru v privátního cloudu ve formátu https://*vcenter-fqdn*/sdk, kde *vcenter-fqdn* je název serveru vCenter.
6. Zadejte přihlašovací údaje pro správce vRealize Automation IaaS, které pro vás vytvořila cloudová podpora.
7. Chcete-li ověřit pověření uživatele, klepněte na tlačítko **Testovat připojení.** Pokud se test nezdaří, ověřte adresu URL, informace o účtu a [název koncového bodu](#verify-the-endpoint-name) a test znovu.
8. Po úspěšném testu kliknutím na **OK** vytvořte koncový bod vSphere.
    ![přístup k on-konfigurace koncového bodu vRA](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Ověření názvu koncového bodu

Chcete-li identifikovat správný název koncového bodu serveru vCenter, postupujte takto:

1. Otevřete příkazový řádek na zařízení IaaS.
2. Změňte adresář na C:\Program Files (x86)\VMware\VCAC\Agents\agent-name, kde *název agenta* je název přiřazený koncovému bodu serveru vCenter.
3. Spusťte následující příkaz.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

Výstup je podobný následujícímu. Hodnota `managementEndpointName` pole je název koncového bodu.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```

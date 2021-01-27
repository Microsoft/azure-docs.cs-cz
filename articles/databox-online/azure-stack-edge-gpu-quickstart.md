---
title: Rychlý Start ke konfiguraci a nasazení GPU Azure Stack Edge | Microsoft Docs
description: Začínáme s nasazením GPU Azure Stack Edge po přijetí zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to quickly deploy Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 2d1bd7d2a4f066311ea01046a8d71e6ecb52c3f5
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919798"
---
# <a name="quickstart-get-started-with-azure-stack-edge-pro-with-gpu"></a>Rychlý Start: Začínáme s Azure Stack Edge pro s grafickým procesorem 

Tento rychlý Start podrobně popisuje požadavky a kroky potřebné k nasazení zařízení GPU Azure Stack Edge pro. Kroky rychlého startu se provádějí na webu Azure Portal a přes místní webové uživatelské rozhraní zařízení. 

Celkový postup by měl trvat přibližně 1,5 hodin. Podrobné pokyny najdete v tématu [kurz: Příprava na nasazení Azure Stack pro grafický procesor Edge pro](azure-stack-edge-gpu-deploy-prep.md#deployment-configuration-checklist). 


## <a name="prerequisites"></a>Požadavky

Před nasazením se ujistěte, že jsou splněné následující předpoklady:

1. Zařízení GPU Azure Stack Edge pro se doručuje na váš web, [rozbalí](azure-stack-edge-gpu-deploy-install.md#unpack-the-device) a připojí se k [racku](azure-stack-edge-gpu-deploy-install.md#rack-the-device). 
1. Nakonfigurujte síť tak, aby zařízení mohla dosáhnout [uvedených vzorů a portů URL](azure-stack-edge-gpu-system-requirements.md#networking-port-requirements). 
1. Máte přístup vlastníka nebo přispěvatele k předplatnému Azure.
1. V Azure Portal můžete přejít na **předplatné domů > > poskytovatelé prostředků > předplatného**. Vyhledejte `Microsoft.DataBoxEdge` a zaregistrujte poskytovatele prostředků. Pokud se chcete zaregistrovat, `Microsoft.Devices` vytvořte IoT Hub prostředek pro nasazení výpočetních úloh, opakujte akci.
1. Ujistěte se, že máte minimálně 2 bezplatných, statických a souvislých IP adres pro uzly Kubernetes a alespoň 1 statickou IP adresu pro IoT Edge službu. Pro každý modul nebo externí službu nasadíte, budete potřebovat 1 dodatečnou IP adresu.
1. V části [Kontrolní seznam nasazení](azure-stack-edge-gpu-deploy-checklist.md) získáte vše, co budete potřebovat pro konfiguraci zařízení. 


## <a name="deployment-steps"></a>Kroky nasazení

1. **Instalace**: Připojte port 1 ke klientskému počítači přes křížený kabel nebo adaptér USB Ethernet. Připojte alespoň jeden další port zařízení pro data, nejlépe 25 přenosů (z portu 3 na PORT 6) do Internetu prostřednictvím přepínače s rychlostí 1 GbE a SFP + měděné kabely. Připojte zadané napájecí šňůry k jednotkám napájení a oddělte možnosti oddělení elektrického napájení. Kliknutím na tlačítko napájení na předním panelu zařízení zapněte.  

    Seznamte se s kompatibilními síťovými kabely a přepínači v části [Cavium FastlinQ 41000 Series](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) a [Mellanox Dual Port 25G ConnectX-4 Channel kompatibilních síťových adaptérů](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products) .

    Toto je minimální konfigurace kabeláže nutná k nasazení zařízení:  ![ zadní rovina kabelového zařízení](./media/azure-stack-edge-gpu-quickstart/backplane-min-cabling-1.png)

2. **Connect**: Nakonfigurujte nastavení IPv4 na adaptéru Ethernet v počítači se statickou IP adresou **192.168.100.5** a podsítí **255.255.255.0**. Otevřete prohlížeč a připojte se k místnímu webovému uživatelskému rozhraní zařízení v https://192.168.100.10 . Může to trvat několik minut. Po zobrazení upozornění na certifikát zabezpečení přejděte na webovou stránku.

3. **Přihlásit** se: Přihlaste se k zařízení s výchozím *Heslo1* hesla. Změňte heslo správce zařízení. Heslo musí obsahovat 8 až 16 znaků a 3 velká písmena, malá písmena, číslice a speciální znaky.

4. **Konfigurace sítě**: přijměte výchozí konfiguraci DHCP pro připojený datový port, pokud máte server DHCP ve vaší síti. V takovém případě zadejte IP adresu, server DNS a výchozí bránu. Další informace o [nastavení sítě](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-network)najdete v tématu.

5. **Konfigurace výpočetní sítě**: vytvořte virtuální přepínač tím, že na svém zařízení povolíte port. Zadejte 2 bezplatné a souvislé statické IP adresy pro uzly Kubernetes ve stejné síti, ve které jste přepínač vytvořili. Poskytněte aspoň 1 statickou IP adresu pro službu IoT Edge hub pro přístup k výpočetním modulům a 1 statickou IP adresu pro každou další službu nebo kontejner, ke kterým chcete získat přístup z vnějšku clusteru Kubernetes. 

    Kubernetes se vyžaduje pro nasazení všech kontejnerových úloh. Přečtěte si další informace o [nastaveních výpočetní sítě](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network).

6. **Konfigurace webového proxy serveru**: Pokud ve svém prostředí používáte webový proxy server, zadejte do nástroje Web proxy server IP `http://<web-proxy-server-FQDN>:<port-id>` . Nastavte ověřování na **žádný**. Další informace najdete v tématu [nastavení webového proxy serveru](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-web-proxy).

7. **Konfigurace zařízení**: zadejte název zařízení a doménu DNS nebo přijměte výchozí nastavení. 

8. **Konfigurace serveru aktualizací**: přijměte výchozí Microsoft Update Server nebo zadejte server služby Windows Server Update Services (WSUS) a cestu k serveru. 

9. **Konfigurace nastavení času**: přijměte výchozí nastavení času nebo nastavte časové pásmo, primární server NTP a sekundární server NTP v místní síti nebo jako veřejné servery.

10. **Konfigurace certifikátů**: Pokud jste změnili název zařízení nebo doménu DNS, musíte vygenerovat certifikáty nebo přidat certifikáty k aktivaci zařízení. 

    - K otestování neprodukčních úloh použijte [možnost generovat certifikáty](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates). 
    - Pokud přenesete vlastní certifikáty včetně podpisových řetězců, [přidejte certifikáty](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) v příslušném formátu. Nejdříve si nezapomeňte nahrát podpisový řetězec. Přečtěte si téma [Vytvoření certifikátů](azure-stack-edge-j-series-create-certificates-tool.md) a [nahrávání certifikátů prostřednictvím místního uživatelského rozhraní](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates).

11. **Activate**: získání aktivačního klíče 

    1. V Azure Portal najdete v části **Přehled prostředků Azure Stack Edge > > nastavení zařízení > aktivovat > vygenerovat klíč**. Zkopírujte klíč. 
    1. V místním webovém uživatelském rozhraní přejdete na **začínáme > aktivovat** a zadáte aktivační klíč. Při použití klíče trvá aktivace zařízení několik minut. `<device-serial-number>`Po zobrazení výzvy k bezpečnému ukládání klíčů zařízení potřebných pro budoucí obnovení Stáhněte soubor. JSON. 

12. **Konfigurace výpočtů**: v Azure Portal najdete **Přehled > zařízení**. Ověřte, že je zařízení **online**. V levém podokně přejdete na **Edge compute > začínáme > konfigurace hraničních výpočtů > výpočetních** prostředků. Poskytněte existující nebo novou službu IoT Hub a počkejte asi 20 minut, než se výpočetní prostředí nakonfiguruje. Přečtěte si další informace o [kurzu: Konfigurace výpočtů na zařízeních GPU Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-compute.md)

Do svého zařízení jste připraveni nasadit výpočetní úlohy [prostřednictvím IoT Edge](azure-stack-edge-gpu-deploy-sample-module-marketplace.md), [prostřednictvím `kubectl` ](azure-stack-edge-gpu-create-kubernetes-cluster.md) nebo [prostřednictvím Kubernetes s povoleným obloukem Azure](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Pokud při instalaci dojde k problémům, Projděte si témata [řešení]()potíží se zařízeními [, problémy s výkonem,](azure-stack-edge-gpu-troubleshoot.md) [problémy s certifikáty](azure-stack-edge-j-series-certificate-troubleshooting.md)nebo Kubernetes problémy. 

## <a name="next-steps"></a>Další kroky

[Instalace GPU Azure Stack Edge pro](./azure-stack-edge-gpu-deploy-install.md)




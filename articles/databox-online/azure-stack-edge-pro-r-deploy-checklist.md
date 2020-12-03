---
title: Kontrolní seznam před nasazením pro nasazení Azure Stack hraničního zařízení pro R | Microsoft Docs
description: Tento článek popisuje informace, které je možné shromáždit před nasazením zařízení Azure Stack Edge pro R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/19/2020
ms.author: alkohli
ms.openlocfilehash: 3a4428b2e6f42facd8230fa4d9f6f637f6fe4659
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531734"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-r-device"></a>Kontrolní seznam nasazení pro zařízení Azure Stack Edge pro R  

Tento článek popisuje informace, které se dají shromáždit před skutečným nasazením zařízení Azure Stack Edge pro R. 

Pomocí následujícího kontrolního seznamu se ujistěte, že máte tyto informace po umístění pro zařízení Azure Stack Edge pro R a před tím, než zařízení přijmete. 

## <a name="deployment-checklist"></a>Kontrolní seznam nasazení 

| Fáze                             | Parametr                                                                                                                                                                                                                           | Podrobnosti                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Správa zařízení               | <li>Předplatné Azure</li><li>Registrovaní poskytovatelé prostředků</li><li>Účet služby Azure Storage</li>|<li>Povoleno pro Azure Stack Edge pro/Data Box Gateway, vlastníka nebo přispěvatele.</li><li>V Azure Portal můžete přejít na **předplatné domů > > poskytovatelé prostředků > předplatného**. Vyhledejte `Microsoft.DataBoxEdge` a zaregistrujte se. Tento postup zopakujte `Microsoft.Devices` , pokud nasazujete úlohy IoT.</li><li>Vyžadovat přístupové přihlašovací údaje</li> |
| Instalace zařízení               | Napájecí kabely v balíčku. <br>Pro nás je dodán konektor SVE 18/3, který je vyhodnocen pro 125 V a 15 Amps pomocí konektoru NEMA 5-15P to C13 (vstup do výstupu). | Další informace najdete v seznamu [podporovaných napájecích šňůr podle země](azure-stack-edge-technical-specifications-power-cords-regional.md) .  |
|                                   | <li>Aspoň 1 X 1-síťový kabel RJ-45 pro port 1  </li><li> Aspoň 1 X 25-GbE SFP + Copper kabel pro port 3, port 4</li>| Zákazník musí tyto kabely obstarat.<br>Úplný seznam podporovaných síťových kabelů, přepínačů a vysílačů pro síťové karty zařízení najdete v tématu Cavium a [Mellanox Dual Port 25G ConnectX-4 pro síťové adaptéry kompatibilní s kanálem](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products) [FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) .| 
| Při prvním připojení zařízení      | <li>Přenosný počítač, jehož nastavení protokolu IPv4 lze změnit. Tento počítač se připojí k portu 1 prostřednictvím přepínače nebo adaptéru USB až k síti Ethernet.  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| Přihlášení zařízení                      | Heslo správce zařízení, od 8 do 16 znaků a obsahuje tři z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky.                                            | Výchozí heslo je *Heslo1* , jehož platnost vyprší při prvním přihlášení.                                                     |
| Nastavení sítě                  | Zařízení obsahuje 2 x 1 GbE, 4 x 25 síťových portů. <li>Port 1 se používá ke konfiguraci nastavení správy. Jeden nebo více datových portů lze připojit a nakonfigurovat. </li><li> Aspoň jedno rozhraní datové sítě z portu 2 – port 6 se musí připojit k Internetu (s připojením k Azure).</li><li> Podporuje se konfigurace DHCP a statická adresa IPv4. | Statická konfigurace IPv4 vyžaduje IP adresu, server DNS a výchozí bránu.   |
| Nastavení výpočetní sítě     | <li>Vyžadovat 2 bezplatné, statické, souvislé IP adresy pro uzly Kubernetes a 1 statickou IP adresu pro IoT Edge službu.</li><li>Pro každou další službu nebo modul, který nasadíte, vyžadovat jednu další IP adresu.</li>| Je podporována pouze statická konfigurace protokolu IPv4.|
| Volitelné Nastavení webového proxy serveru     | <li>Webový proxy server IP/plně kvalifikovaný název domény, port </li><li>Uživatelské jméno a heslo webového proxy serveru</li> | Webový proxy server není při instalaci výpočtů podporován. |
| Nastavení brány firewall a portů        | Pokud používáte bránu firewall, ujistěte se, že [uvedené vzory adres URL a porty](azure-stack-edge-system-requirements.md#networking-port-requirements) jsou povolené pro IP adresy zařízení. |  |
| Doporučil Nastavení času       | Konfigurace časového pásma, primárního serveru NTP, sekundárního serveru NTP. | Konfigurujte primární a sekundární server NTP v místní síti.<br>Pokud místní server není dostupný, můžete nakonfigurovat veřejné servery NTP.                                                    |
| Volitelné Aktualizovat nastavení serveru | <li>Vyžaduje se IP adresa serveru pro aktualizaci v místní síti, cesta k serveru WSUS. </li> | Ve výchozím nastavení se používá veřejný server Windows Update.|
| Nastavení zařízení | <li>Plně kvalifikovaný název domény zařízení (FQDN) </li><li>Doména DNS</li> | |
| Volitelné Certifikáty  | Pokud přenesete vlastní certifikáty včetně podpisových řetězců, [přidejte certifikáty](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#bring-your-own-certificates) v příslušném formátu.| Certifikáty konfigurujte jenom v případě, že změníte název zařízení nebo doménu DNS. |
| Síť VPN  | <!--Need VPN certificate, VPN gateway, firewall setup in Azure,  passphrase and region info VPN scripts. -->   | |
| Šifrování v klidovém případě  | Doporučujeme použít automaticky generovaný šifrovací klíč.   |Pokud používáte vlastní klíč, budete potřebovat 32 znak dlouhý Base-64 kódovaný klíč.  |
| Aktivace  | Vyžadovat aktivační klíč z prostředku Azure Stack Edge pro/Data Box Gateway.    | Po vygenerování vyprší platnost klíče za 3 dny. |

<!--
| (Optional) MAC Address            | If MAC address needs to be approved, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>Další kroky

Připravte se na nasazení [Azure Stack hraničního zařízení pro](azure-stack-edge-pro-r-deploy-prep.md).

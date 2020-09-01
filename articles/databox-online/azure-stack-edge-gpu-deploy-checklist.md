---
title: Kontrolní seznam před nasazením pro nasazení Azure Stack hraniční zařízení | Microsoft Docs
description: Tento článek popisuje informace, které je možné shromáždit před nasazením Azure Stack hraničního zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 065a6595ba89e00310ff41cd63b929f6d6d83222
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084467"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-device"></a>Kontrolní seznam nasazení pro zařízení Azure Stack Edge  

Tento článek popisuje informace, které se dají shromáždit před samotným nasazením zařízení Azure Stack Edge. 

Pomocí následujícího kontrolního seznamu se ujistěte, že máte tyto informace po umístění pro Azure Stack hraniční zařízení a ještě před tím, než zařízení obdržíte. 

## <a name="deployment-checklist"></a>Kontrolní seznam nasazení 

| Fáze                             | Parametr                                                                                                                                                                                                                           | Podrobnosti                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Správa zařízení               | <li>Předplatné Microsoft Azure</li><li>Azure Active Directory Graph API</li><li>Účet Microsoft Azure Storage</li>|<li>Povoleno pro Azure Stack Edge/Data Box Gateway, oprávnění přispěvatele</li><li>Zajistěte přístup správců nebo uživatelů</li><li>Vyžadovat přístupové přihlašovací údaje</li> |
| Instalace zařízení               | Napájecí kabely v balíčku. <br>Pro nás je dodán konektor SVE 18/3, který je vyhodnocen pro 125 V a 15 Amps pomocí konektoru NEMA 5-15P to C13 (vstup do výstupu).                                                                                                                                                                                                          | K dispozici se zařízením.<br>Další informace najdete v seznamu [podporovaných napájecích šňůr podle země](azure-stack-edge-technical-specifications-power-cords-regional.md) .                                                                                        |
|                                   | <li>Aspoň 1 X 1-síťový kabel RJ-45 pro port 1 </li><li> Aspoň 1 X 25-GbE SFP + Copper kabel pro port 3, port 4, port 5 nebo port 6</li>| Zákazník musí tyto kabely obstarat.<br>Úplný seznam podporovaných síťových kabelů, přepínačů a vysílačů pro síťové karty zařízení najdete v části [Cavium FastlinQ 41000 – matice interoperability.](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)                                                                          | 
| Při prvním připojení zařízení      | Port 1 má pro počáteční připojení pevnou IP adresu (192.168.100.10/24). <li>Vyžaduje přenosný počítač pro přímé připojení k portu 1 s IP adresou v síti 192.168.100.0/24.</li><li> Použijte místní uživatelské rozhraní zařízení v: `https://192.168.100.10` pro další konfiguraci.</li>|                                                                                                                   |
| Přihlášení zařízení                      | Heslo správce zařízení musí mít 8 až 16 znaků. <br>Musí obsahovat tři z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky.                                            | Výchozí heslo je *Heslo1* , jehož platnost vyprší při prvním přihlášení.                                                     |
| Nastavení sítě                  | Zařízení obsahuje 2 x 1 GbE, 4 x 25 síťových portů. <li>Port 1 se používá ke konfiguraci nastavení správy. Jeden nebo více datových portů lze připojit a nakonfigurovat. </li><li> Aspoň jedno rozhraní datové sítě z portu 2 – port 6 se musí připojit k Internetu (s připojením k Azure).</li><li> Nastavení IP adres podporují konfiguraci DHCP/static IPv4. | Statická konfigurace IPv4 vyžaduje IP adresu, server DNS a výchozí bránu.                                                                                                                  |
| Nastavení výpočetní sítě     | <li>Vyžadovat 2 statické veřejné IP adresy pro uzly Kubernetes a aspoň 1 statickou IP adresu pro službu Azure Stack Edge hub pro přístup k výpočetním modulům.</li><li>Vyžaduje jednu IP adresu pro každou další službu nebo kontejner, ke kterým se musí přistupovat externě mimo cluster Kubernetes.</li>                                                                                                                       | Je podporována pouze statická konfigurace protokolu IPv4.                                                                      |
| Volitelné Nastavení webového proxy serveru     | <li>Webový proxy server IP/plně kvalifikovaný název domény, port </li><li>Uživatelské jméno a heslo webového proxy serveru</li>                                                                                                                                                                                                    | V současné době se při instalaci výpočtů nepodporuje.                                                                     |
| Nastavení brány firewall a portů        | Použijte [vzory a porty uvedené v adresách URL](azure-stack-edge-system-requirements.md#networking-port-requirements) , které se mají povolit pro IP adresy zařízení.                                                                                                                                                  |                                                                                                                   |
| Volitelné Adresa MAC            | Pokud musí být adresa MAC povolená, Získejte adresu připojeného portu z místního uživatelského rozhraní zařízení. |                                                                                                                   |
| Volitelné Port síťového přepínače    | Zařízení hostuje virtuální počítače Hyper-V pro výpočetní výkon. Některé konfigurace portů síťového přepínače ve výchozím nastavení nevyhovují těmto nastavením.                                                                                                        |                                                                                                                   |
| Doporučil Nastavení času       | Konfigurace časového pásma, primárního serveru NTP, sekundárního serveru NTP.                                                                                                                                                                    | Konfigurujte primární a sekundární server NTP v místní síti.<br>Pokud místní server není dostupný, můžete nakonfigurovat veřejné servery NTP.                                                    |
| Volitelné Aktualizovat nastavení serveru | <li>Vyžaduje se IP adresa serveru pro aktualizaci v místní síti, cesta k serveru WSUS. </li> | Ve výchozím nastavení se používá veřejný server Windows Update.|
| Nastavení zařízení                   | <li>Název zařízení zaregistrovaný v rámci organizace DNS </li><li>Doména DNS</li> |                                                                                                                   |
| Volitelné Certifikáty                      | Použití certifikátů generovaných zařízením <br><br> Pokud přidáváte vlastní certifikáty, budete potřebovat: <li>Důvěryhodný kořenový podpisový certifikát ve formátu DER s *příponou. cer* </li><li>Certifikáty koncového bodu ve formátu *PFX*</li>|Certifikáty koncového bodu zahrnují Azure Resource Manager, BLOB Storage, místní webové uživatelské rozhraní.                                                                                                                   |
| Aktivace                        | Vyžadovat aktivační klíč z Azure Stackho okraje nebo prostředku Data Box Gateway                                                                                                                                                       | Po vygenerování vyprší platnost klíče za 3 dny.                                                                        |


## <a name="next-steps"></a>Další kroky

Připravte se na nasazení [Azure Stack hraničního zařízení](azure-stack-edge-gpu-deploy-prep.md).




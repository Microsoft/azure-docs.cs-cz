---
title: Konfigurace sítě Ethernet pro BareMetal pro Oracle
description: Přečtěte si o konfiguraci rozhraní Ethernet pro instance BareMetal pro úlohy Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: e98af51075d5193cab14d18f1cdb1f431c8fa892
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559068"
---
# <a name="ethernet-configuration-of-baremetal-for-oracle"></a>Konfigurace sítě Ethernet pro BareMetal pro Oracle

V tomto článku se podíváme na konfiguraci rozhraní Ethernet pro instance BareMetal pro úlohy Oracle.

Každá zřízená instance BareMetal pro Oracle je předem nakonfigurovaná se sadami rozhraní Ethernet. Rozhraní sítě Ethernet jsou rozdělená do čtyř typů:

- Používá se pro nebo ke klientskému přístupu.
- Používá se pro komunikaci mezi uzly. Toto rozhraní je nakonfigurované na všech serverech bez ohledu na požadovanou topologii. Používá se pouze pro scénáře škálování na více instancí.
- Používá se pro připojení mezi uzly.
- Slouží k nastavení zotavení po havárii (DR) a připojení k globálnímu přístupu pro připojení mezi jednotlivými oblastmi.

## <a name="architecture"></a>Architektura

Následující diagram znázorňuje architekturu předem nakonfigurovaných rozhraní Ethernet BareMetal infrastruktury. 

[![Diagram znázorňující architekturu předem nakonfigurovaných rozhraní Ethernet pro úlohy Oracle.](media/oracle-baremetal-ethernet/architecture-ethernet.png)](media/oracle-baremetal-ethernet/architecture-ethernet.png#lightbox)

Výchozí konfigurace obsahuje jedno rozhraní IP klienta (eth1), které se připojuje z Azure Virtual Network (VNET), pomocí kterých můžete Secure Shell (SSH) získat přístup k instanci BareMetal.

> [!NOTE]
> Pro jiné klientské rozhraní (ETH10) z jiné virtuální sítě Azure kontaktujte Microsoft CSA, aby odeslal žádost o služby. Například pokud chcete, aby byly vývojové a testovací prostředí a také prostředí pro výrobu a zotavení po havárii.

| **Logické rozhraní NIC** | **Název s operačním systémem RHEL** | **Případ použití** |
| --- | --- | --- |
| A | eth1. tenant | Klient do instance BareMetal |
| C | ETH2. tenant | Z uzlu do úložiště; podporuje koordinaci a přístup k řadičům úložiště pro správu prostředí úložiště. |
| B | Eth3. tenant | Uzel na uzel (privátní propojení) |
| C | eth4. tenant | Rezervované/iSCSI |
| C | eth5. tenant | Rezervované a zaprotokolované zálohování |
| C | eth6. tenant | Zálohování Node-to-storage_Data (RMAN, Snapshot) |
| C | eth7. tenant | Node-to-storage_dNFS-pri; poskytuje připojení k poli úložiště NetApp. |
| C | eth8. tenant | Node-to-storage_dNFS-s; poskytuje připojení k poli úložiště NetApp. |
| D | eth9. tenant | Připojení DR pro nastavení globálního přístupu pro přístup k BMI v jiné oblasti. |
| A | \*ETH10. tenant | \* Klient do instance BareMetal
 |

V případě potřeby můžete definovat více karet síťového rozhraní (NIC). Konfigurace stávajících síťových karet se ale *nedají* změnit.

## <a name="usage-rules"></a>Pravidla používání

U instancí BareMetal bude mít výchozí hodnota devět přiřazených IP adres na čtyřech logických síťových kartách. Platí následující pravidla použití:

- Síť Ethernet "A" by měla mít přiřazenou IP adresu mimo rozsah adres fondu IP adres, který jste odeslali společnosti Microsoft. Tato IP adresa by se neměla udržovat v adresáři etc/hosts operačního systému.
- Síť Ethernet "B" by měla být zachována výhradně v adresáři etc/hosts pro komunikaci mezi různými instancemi. Udržujte tyto IP adresy v konfiguracích certifikátů Oracle reálné aplikace na více systémů jako IP adresy, které se používají pro konfiguraci mezi uzly.
- Síť Ethernet "C" by měla mít přiřazenou IP adresu, která se používá ke komunikaci s úložištěm NFS. Tento typ adresy by neměl být udržován v adresáři etc/hosts.
- Síť Ethernet "D" by se měla používat výhradně pro nastavení globálního přístupu k přístupu k instancím BareMetal v oblasti zotavení po havárii.

## <a name="next-step"></a>Další krok

Přečtěte si další informace o infrastruktuře BareMetal pro architekturu Oracle.

> [!div class="nextstepaction"]
> [Architektura infrastruktury BareMetal pro Oracle](oracle-baremetal-architecture.md)

---
title: Možnosti zapojení kabeláže Azure Data Boxu | Microsoft Docs
description: Přečtěte si o různých způsobech, jak kabelovat Azure Data Box pro přenos dat pomocí portu pro správu nebo datového portu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 10/24/2018
ms.author: alkohli
ms.openlocfilehash: a2c4cce492c48ebe6e09ad142f97bc6377a8a5e1
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94334597"
---
# <a name="cabling-options-for-your-azure-data-box"></a>Možnosti zapojení kabeláže Azure Data Boxu

Tento článek popisuje různé způsoby zapojení kabeláže Azure Data Boxu pro přenos dat.

## <a name="transfer-via-mgmt-port"></a>Přenos přes port MGMT

Tato možnost představuje naprosto minimální konfiguraci Data Boxu. Pro správu i data můžete nakonfigurovat pouze port MGMT.

Než začnete, ujistěte se, že máte následující:

- Ethernetový kabel RJ45 pro MGMT.
- Zdroj dat s [podporovaným operačním systémem](data-box-system-requirements.md#supported-operating-systems-for-clients).

Při zapojování kabeláže zařízení postupujte následovně.

1. Pomocí kabelu RJ45 propojte port MGMT se serverem, na kterém jsou uložená data.

    ![Nastavení s jedním portem](media/data-box-cable-options/cabling-mgmt-only.png)

2. Na serveru nastavte:

    - **IP adresu** na 192.168.100.5
    - **Podsíť** na 255.255.255.0

3. Přejděte do místního webového uživatelského rozhraní zařízení na adrese 192.168.100.10. Přihlaste se k Data Boxu a odemkněte ho pomocí hesla pro odemčení z webu Azure Portal.


## <a name="transfer-via-data-port-with-static-ips"></a>Přenos přes datový port se statickými IP adresami

Pro svůj Data Box můžete nakonfigurovat dva porty: port MGMT pro provoz správy a jeden z datových portů pro data. Mezi datové porty patří DATA 1, DATA 2 nebo DATA 3.

Důrazně doporučujeme, abyste v případě, že konfigurujete pouze jeden datový port, použili port 10 GbE, jako je DATA 1 nebo DATA 2. Port 1 GbE by výrazně prodloužil dobu potřebnou k přenosu dat.

Než začnete, ujistěte se, že máte následující:

- Ethernetový kabel RJ45 pro MGMT.
- Měděný kabel 10 GbE SFP+ Twinax pro každý datový port 10 GbE, který chcete připojit.
- Jeden nebo několik zdrojů dat s [podporovaným operačním systémem](data-box-system-requirements.md#supported-operating-systems-for-clients).

### <a name="option-1---initial-setup-via-server"></a>Možnost 1 – Počáteční nastavení prostřednictvím serveru

Při zapojování kabeláže zařízení postupujte následovně.

1. Pomocí ethernetového kabelu RJ45 propojte server přímo s portem MGMT pro konfiguraci.
2. Propojte datový port se serverem, který funguje jako zdroj dat – v případě portu DATA 3 použijte kabel RJ45 a v případě portu DATA 1 nebo DATA 2 použijte kabel SFP+. Pro zajištění co nejlepšího výkonu doporučujeme použít port DATA 1 nebo DATA 2 10 GbE.
3. Na serveru nastavte:

   - **IP adresu** na 192.168.100.5
   - **Podsíť** na 255.255.255.0

     ![Instalace se dvěma porty](media/data-box-cable-options/cabling-2-port-setup.png)

3. Přejděte do místního webového uživatelského rozhraní zařízení na adrese 192.168.100.10. Přihlaste se k Data Boxu a odemkněte ho pomocí hesla pro odemčení z webu Azure Portal.
4. K datovým portům, které jste nakonfigurovali, přiřaďte statické IP adresy.

### <a name="option-2---initial-setup-via-separate-computer"></a>Možnost 2 – Počáteční nastavení prostřednictvím samostatného počítače

Při zapojování kabeláže zařízení postupujte následovně.

1. Pro konfiguraci použijte ethernetový kabel RJ45 od samostatného počítače přímo k portu pro správu.
2. Propojte datový port se serverem – v případě portu DATA 3 použijte kabel RJ45 a v případě portu DATA 1 nebo DATA 2 použijte kabel SFP+. Pro zajištění co nejlepšího výkonu doporučujeme použít port DATA 1 nebo DATA 2 10 GbE. Datové porty jsou k serveru s daty připojené přes přepínač 10 GbE.
3. Na přenosném počítači, pomocí kterého se připojujete k zařízení, nakonfigurujte adaptér sítě Ethernet:

   - **IP adresa:** 192.168.100.5
   - **Podsíť:** 255.255.255.0
  
   ![Nastavení dvou portů s přepínačem](media/data-box-cable-options/cabling-with-static-ip.png)

3. Přejděte do místního webového uživatelského rozhraní zařízení na adrese 192.168.100.10. Přihlaste se k Data Boxu a odemkněte ho pomocí hesla pro odemčení z webu Azure Portal.
4. Zjistěte IP adresy přiřazené serverem DHCP.

## <a name="transfer-via-data-port-with-static-ips-using-a-switch"></a>Přenos přes datový port se statickými IP adresami s využitím přepínače 

Tuto konfiguraci použijte pro více zdrojů dat v sítích 1 GbE a 10 GbE.

Než začnete, ujistěte se, že máte následující:

- Ethernetový kabel RJ45 pro MGMT.
- Měděný kabel 10 GbE SFP+ Twinax pro každý datový port 10 GbE, který chcete připojit.
- Jeden nebo několik zdrojů dat s [podporovaným operačním systémem](data-box-system-requirements.md#supported-operating-systems-for-clients). Tyto zdroje dat můžou být v různých sítích, například v sítích 1 GbE nebo 10 GbE.

Při zapojování kabeláže zařízení postupujte následovně.

1. Pomocí ethernetového kabelu RJ45 propojte server přímo s portem MGMT pro konfiguraci.
2. Propojte datový port se serverem – v případě portu DATA 3 použijte kabel RJ45 a v případě portu DATA 1 nebo DATA 2 použijte kabel SFP+. Pro zajištění co nejlepšího výkonu doporučujeme použít port DATA 1 nebo DATA 2 10 GbE.
3. Na přenosném počítači, pomocí kterého se připojujete k zařízení, nakonfigurujte adaptér sítě Ethernet:

   - **IP adresa:** 192.168.100.5
   - **Podsíť:** 255.255.255.0

     ![Nastavení dvou portů s přepínačem 2](media/data-box-cable-options/cabling-with-switch-static-ip.png)

3. Přejděte do místního webového uživatelského rozhraní zařízení na adrese 192.168.100.10. Přihlaste se k Data Boxu a odemkněte ho pomocí hesla pro odemčení z webu Azure Portal.
4. K datovým portům, které jste nakonfigurovali, přiřaďte statické IP adresy.


## <a name="transfer-via-data-port-in-a-dhcp-environment"></a>Přenos přes datový port v prostředí DHCP

Tuto konfiguraci použijte, pokud vaše zařízení bude v prostředí DHCP.

Než začnete, ujistěte se, že máte následující:

- Kabel RJ45, pokud chcete připojit port DATA 1.
- Měděný kabel 10 GbE SFP+ Twinax pro každý datový port 10 GbE, který chcete připojit.
- Jeden nebo několik zdrojů dat s [podporovaným operačním systémem](data-box-system-requirements.md#supported-operating-systems-for-clients). Tyto zdroje dat můžou být v různých sítích, například v sítích 1 GbE nebo 10 GbE.

Při zapojování kabeláže zařízení postupujte následovně.

1. Propojte kabel RJ45 nebo SFP+ přes přepínač (na kterém je přístupný server DHCP) se serverem.

    ![Nastavení dvou portů pomocí přepínače 3](media/data-box-cable-options/cabling-dhcp-data-only.png)

2. Pomocí serveru DHCP nebo serveru DNS zjistěte IP adresu.
3. Na serveru ve stejné síti přejděte pomocí IP adresy přiřazené serverem DHCP do místního webového uživatelského rozhraní zařízení. Přihlaste se k Data Boxu a odemkněte ho pomocí hesla pro odemčení z webu Azure Portal.

## <a name="next-steps"></a>Další kroky

- Po zapojení kabeláže zařízení přejděte k tématu [Kopírování dat do Azure Data Boxu](data-box-deploy-copy-data.md).

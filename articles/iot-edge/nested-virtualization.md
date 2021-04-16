---
title: Vnořená virtualizace pro Azure IoT Edge pro Linux ve Windows | Microsoft Docs
description: Přečtěte si, jak navigovat vnořenou virtualizaci v Azure IoT Edge pro Linux ve Windows.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 4f01362fd9342c1f508f165b34e121a11e8d07e2
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496584"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Vnořená virtualizace pro Azure IoT Edge pro Linux ve Windows
Existují dvě formy vnořené virtualizace, která je kompatibilní s Azure IoT Edge pro Linux ve Windows. Uživatelé můžou zvolit nasazení prostřednictvím místního virtuálního počítače nebo virtuálního počítače Azure. Tento článek umožní uživatelům jasnosti, které možnosti jsou pro svůj scénář nejvhodnější, a poskytují přehled o požadavcích na konfiguraci.

> [!NOTE]
>
> Zajistěte, aby byla pro vnořenou virtualizaci povolena jedna [možnost sítě](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) . Pokud to neuděláte, dojde k chybám při instalaci EFLOW. 

## <a name="deployment-on-local-vm"></a>Nasazení na místním virtuálním počítači
Toto je základní přístup k libovolnému virtuálnímu počítači s Windows, který je hostitelem Azure IoT Edge pro Linux ve Windows. V takovém případě je třeba před zahájením nasazení povolit vnořenou virtualizaci. Další informace o tom, jak nakonfigurovat tento scénář, najdete [v tématu spuštění technologie Hyper-v ve virtuálním počítači se vnořenou virtualizací](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) .

Pokud používáte Windows Server, ujistěte se, že jste [nainstalovali roli Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="deployment-on-azure-vms"></a>Nasazení na virtuálních počítačích Azure
Pokud se rozhodnete k nasazení na virtuální počítače Azure, pamatujte na to, že není k dispozici žádný externí přepínač. Azure IoT Edge pro Linux ve Windows není také kompatibilní na virtuálním počítači Azure, na kterém je spuštěná SKU serveru, pokud se nespustí konkrétní skript, který zahájí výchozí přepínač. Další informace o tom, jak zobrazit výchozí přepínač, najdete níže v [části Windows Server](#windows-server) . 

> [!NOTE]
>
> Všechny virtuální počítače Azure, které by měly hostovat EFLOW, musí být virtuální počítač, který [podporuje vnořenou virtualizaci](../virtual-machines/acu.md) .


## <a name="limited-use-of-external-switch"></a>Omezené použití externího přepínače
V jakémkoli scénáři, kdy virtuální počítač nemůže získat IP adresu prostřednictvím externího přepínače, funkce nasazení automaticky použije interní výchozí přepínač pro nasazení. To znamená, že správu Azure IoT Edge pro virtuální počítače se systémem Linux lze spustit pouze na samotném cílovém zařízení (tj. připojení k virtuálnímu počítači s Azure IoT Edge pro Linux prostřednictvím rozšíření SSH služby WAC v prostředí PowerShell je možné pouze na místním hostiteli).

## <a name="windows-server"></a>Windows Server
U uživatelů Windows serveru mějte na paměti, že Azure IoT Edge pro Linux ve Windows automaticky nepodporuje výchozí přepínač.

* Důsledky pro místní virtuální počítače: Ujistěte se, že virtuální počítač EFLOW může získat IP adresu prostřednictvím externího přepínače.

* Důsledky pro virtuální počítače Azure: vzhledem k tomu, že na virtuálních počítačích Azure není žádný externí přepínač, není možné nasazovat EFLOW před nastavením interního přepínače na serveru.

Ve výchozím nastavení není žádný výchozí přepínač na SKU serveru (bez ohledu na to, jestli se skladová položka serveru spouští na virtuálním počítači Azure nebo ne). Při nasazování na virtuální počítač Azure, kde se nedá použít externí přepínač, je potřeba nastavit a nakonfigurovat výchozí přepínač ručně, než Azure IoT Edge pro Linux v nasazení Windows. Naše funkce nasazení se vztahují k tomu, že vyžaduje konfiguraci protokolu IP pro interní přepínač, konfiguraci překladu adres (NAT) a instalaci a konfiguraci serveru DHCP. Naše funkce nasazení ve verzi Public Preview uvádí, že se s těmito nastaveními nezhoršují konfigurace sítě při produktivních nasazeních.

* Příslušné informace o tom, jak nastavit výchozí přepínač ručně, najdete tady: [Jak povolit vnořenou virtualizaci v Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)
* Dokumentaci k nastavení serveru DHCP pro tento scénář najdete tady: [nasazení DHCP pomocí Windows PowerShellu](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps) .

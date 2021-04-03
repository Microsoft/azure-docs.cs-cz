---
title: Podpora vzdálené práce Azure Firewall
description: Tento článek ukazuje, jak Azure Firewall můžou podporovat požadavky na vzdálené pracovní síly.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: 3c0e2033ee559af38a6816bdfa611eea86b14dea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94658311"
---
# <a name="azure-firewall-remote-work-support"></a>Podpora vzdálené práce Azure Firewall

Azure Firewall je spravovaná cloudová služba zabezpečení sítě, která chrání vaše prostředky virtuální sítě Azure. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností v cloudu.

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>Podpora nasazení infrastruktury virtuálních klientských počítačů (VDI)

Práce z domácích zásad vyžaduje mnoho organizací IT, aby bylo možné řešit zásadní změny kapacity, sítě, zabezpečení a zásad správného řízení. Zaměstnanci nejsou chráněni pomocí vrstvených zásad zabezpečení přidružených k místním službám při práci z domova. Nasazení infrastruktury virtuálních klientských počítačů (VDI) v Azure může organizacím rychle reagovat na toto měnící se prostředí. Ale potřebujete způsob, jak chránit příchozí a odchozí internetový přístup k těmto nasazením VDI a z nich. K ochraně nasazení infrastruktury virtuálních klientských počítačů můžete použít Azure Firewall [pravidla DNAT](rule-processing.md) spolu s funkcemi filtrování na základě analýzy [hrozeb](threat-intel.md) .

## <a name="azure-windows-virtual-desktop-support"></a>Podpora virtuálních počítačů Azure s Windows

Windows Virtual Desktop je komplexní služba pro desktopovou a aplikační virtualizaci běžící v Azure. Je to jediná infrastruktura virtuálních klientských počítačů (VDI), která nabízí zjednodušenou správu, Windows 10 s více relacemi, optimalizace pro Microsoft 365 aplikací pro podniky a podporu pro prostředí vzdálené plochy (RDS). Stolní počítače a aplikace Windows můžete v Azure nasadit a škálovat v řádu minut a získat integrované funkce zabezpečení a dodržování předpisů. Virtuální počítač s Windows nevyžaduje, abyste otevřeli žádný příchozí přístup k vaší virtuální síti. Je ale potřeba, abyste povolili sadu odchozích síťových připojení pro virtuální počítače virtuálních počítačů s Windows, které běží ve vaší virtuální síti. Další informace najdete v tématu [použití Azure firewall k ochraně nasazení virtuálních ploch](protect-windows-virtual-desktop.md)v systému Windows.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [virtuálním počítači s Windows](../virtual-desktop/index.yml).
---
title: Základy architektury v Azure Lab Services | Microsoft Docs
description: Tento článek se zabývá základními prostředky používanými v rámci testovacích služeb a základní architekturou testovacího prostředí.
author: emaher
ms.topic: overview
ms.date: 09/16/2020
ms.author: enewman
ms.openlocfilehash: ac390fb6624ffc762f77b676c778165e7382f73f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334034"
---
# <a name="architecture-fundamentals-in-azure-lab-services"></a>Základy architektury v Azure Lab Services

Azure Lab Services je řešení SaaS (software jako služba), což znamená, že prostředky, které služba Lab Services potřebuje, se zpracují za vás. Tento článek se zabývá základními prostředky používanými v rámci testovacích služeb a základní architekturou testovacího prostředí.  

Azure Lab Services poskytuje několik oblastí, které vám umožní používat vlastní prostředky ve spojení s testovacími službami.  Další informace o použití virtuálních počítačů ve vaší vlastní síti najdete v tématu Jak [vytvořit partnerský vztah k virtuální síti](how-to-connect-peer-virtual-network.md).  Pokud chcete znovu použít image z Galerie sdílených imagí, přečtěte si téma Jak [připojit sdílenou galerii imagí](how-to-attach-detach-shared-image-gallery.md).

Níže je uvedená základní architektura prostředí učebny.  Účet testovacího prostředí je hostovaný v rámci vašeho předplatného. Virtuální počítače studenta spolu s prostředky potřebnými k podpoře virtuálních počítačů se hostují v rámci předplatného vlastněných testovacími službami. Pojďme pohovořit o tom, co je v předplatných služby testovacího prostředí podrobněji.

![Architektura základní architektury učebny Labs](./media/classroom-labs-fundamentals/labservices-basic-architecture.png)

## <a name="hosted-resources"></a>Hostované prostředky

Prostředky potřebné pro běh prostředí učebny se hostují v jednom z předplatných Azure spravovaných Microsoftem.  Prostředky zahrnují šablonu virtuálního počítače pro instruktora, virtuální počítač pro každého studenta a položky související se sítí, jako je třeba nástroj pro vyrovnávání zatížení, virtuální síť a skupina zabezpečení sítě.  Tato předplatná se monitorují pro podezřelé aktivity.  Je důležité si uvědomit, že toto monitorování je k virtuálním počítačům prováděno externě prostřednictvím rozšíření virtuálních počítačů nebo monitorování síťového vzoru.  Pokud je povolené [vypnutí při odpojení](how-to-enable-shutdown-disconnect.md) , na virtuálním počítači je povolené diagnostické rozšíření. Rozšíření umožňuje, aby služby testovacího prostředí byly informované o události odpojení relace protokolu RDP (Remote Desktop Protocol).

## <a name="virtual-network"></a>Virtual Network

Každé testovací prostředí je izolované podle vlastní virtuální sítě.  Pokud má testovací prostředí [virtuální síť s partnerským vztahem](how-to-connect-peer-virtual-network.md), pak je každé testovací prostředí izolované s vlastní podsítí.  Studenti se ke svému virtuálnímu počítači připojí přes Nástroj pro vyrovnávání zatížení.  Žádné virtuální počítače studenta nemají veřejnou IP adresu; mají jenom privátní IP adresu.  Připojovací řetězec pro studenta bude veřejná IP adresa nástroje pro vyrovnávání zatížení a náhodným portem mezi 49152 a 65535.  Příchozí pravidla nástroje pro vyrovnávání zatížení předávají připojení v závislosti na operačním systému na port 22 (SSH) nebo port 3389 (RDP) příslušného virtuálního počítače. NSG zabraňuje vnějšímu provozu na jiných portech.

## <a name="access-control-to-the-virtual-machines"></a>Řízení přístupu k virtuálním počítačům

Služba testovacího prostředí zpracovává schopnost studenta provádět akce, jako je spuštění a zastavení na svých virtuálních počítačích.  Také řídí přístup k informacím o připojení virtuálních počítačů.

Služba testovacího prostředí také zpracovává registraci studentů do služby. Aktuálně jsou k dispozici dvě různá nastavení přístupu: omezená a neomezená. Další informace najdete v článku věnovaném [správě testovacích uživatelů](how-to-configure-student-usage.md#send-invitations-to-users) . Omezený přístup znamená, že testovací služby před povolením přístupu ověří, že se studenti přidávají jako uživatel. Neomezeně znamená, že každý uživatel se může zaregistrovat, dokud má odkaz na registraci a v testovacím prostředí existuje kapacita. Neomezeně může být užitečné pro události Hackathon.

Virtuální počítače studenta hostované v prostředí učebny mají uživatelské jméno a heslo nastavené autorem testovacího prostředí.  Autor testovacího prostředí může alternativně dovolit registrovaným studentům při prvním přihlášení zvolit vlastní heslo.  

## <a name="next-steps"></a>Další kroky

Další informace o funkcích dostupných v testovacích službách najdete v tématu [Azure Lab Services koncepty](classroom-labs-concepts.md) a [Azure Lab Services přehled](classroom-labs-overview.md).

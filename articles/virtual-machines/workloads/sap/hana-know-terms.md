---
title: Znát podmínky SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Znát podmínky SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6785ae821f701121185f0064c6317c69d50191ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617054"
---
# <a name="know-the-terms"></a>Seznámení s podmínkami

V příručce architektura a technické nasazení se široce používá několik běžných definic. Všimněte si následujících výrazů a jejich významu:

- **IaaS**: Infrastruktura jako služba.
- **PaaS**: Platforma jako služba.
- **SaaS**: Software jako služba.
- **Komponenta SAP**: Samostatná aplikace SAP, například ERP Central Component (ECC), Business Warehouse (BW), Solution Manager nebo Enterprise Portal (EP). Komponenty SAP mohou být založeny na tradičních technologiích ABAP nebo Java nebo na aplikaci nezaložené na aplikaci, jako jsou obchodní objekty.
- **Prostředí SAP**: Jedna nebo více součástí SAP logicky seskupených k provádění obchodních funkcí, jako je vývoj, zajištění kvality, školení, zotavení po havárii nebo výroba.
- **Sap krajina**: Odkazuje na celé prostředky SAP v oblasti IT. Prostředí SAP zahrnuje všechna výrobní a neprodukční prostředí.
- **Systém SAP**: Kombinace vrstvy DBMS a aplikační vrstvy například vývojového systému SAP ERP, testovacího systému SAP BW a výrobního systému SAP CRM. Nasazení Azure nepodporují rozdělení těchto dvou vrstev mezi místní a Azure. Systém SAP se nasadí místně nebo se nasadí v Azure. Můžete nasadit různé systémy prostředí SAP do Azure nebo místně. Například můžete nasadit sap CRM vývojových a testovacích systémů v Azure, zatímco nasazujete produkční systém SAP CRM místně. Pro SAP HANA v Azure (velké instance) se má za to, že hostujete aplikační vrstvu SAP systémů SAP ve virtuálních počítačích a související instanci SAP HANA na jednotce v razítku SAP HANA v Azure (velké instance).
- **Razítko velké instance:** Zásobník hardwarové infrastruktury, který má certifikaci SAP HANA TDI a je vyhrazený pro spouštění instancí SAP HANA v Azure.
- **SAP HANA v Azure (velké instance):** Oficiální název nabídky v Azure pro spuštění instancí HANA v hardwaru s certifikací SAP HANA TDI, který se nasadí v razítkách velkých instancí v různých oblastech Azure. Související termín *HANA Velká instance* je zkratka pro SAP HANA v Azure *(velké instance)* a je široce používán v této příručce technické nasazení.
- **Mezi místními**: Popisuje scénář, kde se virtuální počítače nasazují do předplatného Azure, které má připojení site-to-site, multi-site nebo Azure ExpressRoute mezi místními datovými centry a Azure. V běžné dokumentaci Azure tyto druhy nasazení jsou také popsány jako scénáře mezi místními. Důvodem připojení je rozšíření místních domén, místníslužby Azure Active Directory/OpenLDAP a místní DNS do Azure. Místní prostředí se rozšiřuje na prostředky Azure předplatných Azure. S tímto rozšířením virtuálních počítačích může být součástí místní domény. 

   Uživatelé domény místní domény mohou přistupovat k serverům a spouštět služby na těchto virtuálních počítačích (například služby DBMS). Komunikace a překlad názvů mezi virtuálními počítači nasazenými místně a virtuálními počítači nasazenými v Azure je možné. Tento scénář je typický pro způsob, jakým se nasadí většina prostředků SAP. Další informace najdete v tématu [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a Vytvoření virtuální sítě s [připojením k webu pomocí webu na webu pomocí portálu Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Tenant**: Zákazník nasazený v razítku velké instance HANA se izoluje do *tenanta.* Klient je izolovaný v síti, úložiště a výpočetní vrstvy od ostatních klientů. Jednotky úložiště a výpočetní jednotky přiřazené různým klientům se nemohou navzájem vidět ani spolu komunikovat na úrovni razítka velké instance HANA. Zákazník se může rozhodnout, že bude mít nasazení do různých klientů. I v takovém případě neexistuje žádná komunikace mezi klienty na úrovni razítka velké instance HANA.
- **Kategorie skladové položky**: Pro velké instance HANA jsou nabízeny následující dvě kategorie skladových položek:
    - **Třída Typu I**: S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 a S224m
    - **Třída typu II:** S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm a S960m
- **Razítko**: Definuje velikost interního nasazení společnosti Microsoft velkých instancí HANA. Před nasazením jednotek velké instance HANA je třeba nasadit razítko velké instance HANA, které se skládá z výpočetních, síťových a úložných racků, v umístění datového centra. Takové nasazení se nazývá hana velké instance razítko nebo z revize 4 (viz níže) na používáme alternativní termín **velké instance řádku**
- **Revize**: Existují dvě různé revize razítek pro razítka velké instance HANA. Ty se liší architekturou a blízkostí hostitelů virtuálních strojů Azure
    - "Revize 3" (zj 3): je původní návrh, který byl nasazen od poloviny roku 2016
    - "Revize 4" (Rev 4): je nový návrh, který může poskytnout bližší blízkost k hostitelům virtuálních strojů Azure a s nižší latencí sítě mezi virtuálními počítači Azure a jednotkami velké instance HANA 

K dispozici je celá řada dalších prostředků, jak nasadit úlohu SAP v cloudu. Pokud máte v plánu provést nasazení SAP HANA v Azure, musíte mít zkušenosti s principy Azure IaaS a nasazení úloh SAP na Azure IaaS. Než budete pokračovat, další informace najdete [v tématu Použití řešení SAP na virtuálních počítačích Azure.](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

**Další kroky**
- Odkazovat [hli certifikace](hana-certification.md)
---
title: Poznejte SAP HANA v Azure (velké instance) | Microsoft Docs
description: Seznamte se s podmínkami SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fec9f18404fc45f4cf69cc13b1602f818dbddfaf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099739"
---
# <a name="know-the-terms"></a>Seznámení s podmínkami

V průvodci architekturou a technickým nasazením se často používá několik běžných definic. Všimněte si následujících podmínek a jejich významu:

- **IaaS**: Infrastruktura jako služba.
- **PaaS**: Platforma jako služba.
- **SaaS**: Software jako služba.
- **Součást SAP**: Jednotlivá aplikace SAP, jako je například součást ERP Central (ECC), obchodní sklad (ČERNOBÍLý), správce řešení nebo Enterprise Portal (EP). Komponenty SAP můžou být založené na tradičních technologiích ABAP a Java nebo na NetWeaver aplikaci, jako jsou třeba obchodní objekty.
- **Prostředí SAP**: Jedna nebo více komponent SAP jsou logicky seskupeny k provádění obchodních funkcí, jako je vývoj, zabezpečování kvality, školení, zotavení po havárii nebo produkce.
- **SAP na šířku**: Odkazuje na celé prostředky SAP na svém IT na svém IT oddělení. SAP na šířku zahrnuje všechna produkční a neprodukční prostředí.
- **Systém SAP**: Kombinace vrstvy a aplikační vrstvy systému DBMS, například vývojového systému SAP ERP, SAP BW testovacího systému a produkčního systému SAP CRM. Nasazení Azure nepodporuje dělení těchto dvou vrstev mezi místními a Azure. Systém SAP je buď nasazený místně, nebo je nasazený v Azure. Můžete nasadit různé systémy SAP na šířku do Azure nebo do místního prostředí. Můžete například nasadit vývojové a testovací systémy SAP CRM do Azure, zatímco nasazujete produkční systém SAP CRM v místním prostředí. V případě SAP HANA v Azure (velké instance) je zamýšlená možnost hostování aplikace SAP pro systémy SAP na virtuálních počítačích a související instanci SAP HANA na jednotce v SAP HANA na platformě Azure (velké instance).
- **Razítko velké instance**: Sada hardwarových infrastruktur, která je SAP HANA s certifikací TDI a vyhrazená ke spouštění SAP HANA instancí v rámci Azure.
- **SAP HANA v Azure (velké instance):** Oficiální název nabídky v Azure, ve kterém se spouštějí instance HANA v SAP HANA hardwaru TDI, který je nasazený ve velkých názvech instancí v různých oblastech Azure. Související *Velká instance Hana* je krátká pro *SAP HANA v Azure (velké instance)* a v této technické příručce pro nasazení se používá často.
- **Mezi různými**místy: Popisuje situaci, kdy se virtuální počítače nasazují do předplatného Azure, které má připojení typu Site-to-site nebo Azure ExpressRoute mezi místními datovými centry a Azure. V běžné dokumentaci k Azure jsou tyto typy nasazení popsány také jako mezi místními scénáři. Důvodem připojení je rozšiřování místních domén, místních Azure Active Directory/OpenLDAP a místní DNS do Azure. Místní orientace se rozšiřuje na prostředky Azure předplatných Azure. S tímto rozšířením můžou být virtuální počítače součástí místní domény. 

   Uživatelé domény v místní doméně mají přístup k serverům a spouštějí služby na těchto virtuálních počítačích (například služby DBMS). Je možné komunikovat a překlad názvů mezi virtuálními počítači nasazenými místně a virtuálními počítači nasazenými v Azure. Tento scénář je typický pro způsob, jakým se nasazují většina prostředků SAP. Další informace najdete v tématu [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a [vytvoření virtuální sítě s připojením typu Site-to-site pomocí Azure Portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Tenant**: Zákazník nasazený v rámci razítka velké instance HANA získá izolaci do *tenanta.* Tenant je izolovaný v síti, úložišti a výpočetní vrstvě z jiných tenantů. Úložiště a výpočetní jednotky přiřazené různým klientům se nemůžou vzájemně zobrazit ani vzájemně komunikovat na úrovni razítka velké instance služby HANA. Zákazník se může rozhodnout, že mají nasazení v různých klientech. I potom neexistuje žádná komunikace mezi klienty na úrovni razítka velké instance HANA.
- **Kategorie SKU**: Pro velkou instanci HANA jsou k dispozici tyto dvě kategorie SKU:
    - **Třída Type I**: S72, S72m, S96, S144, S144m, S192, S192m a S192xm
    - **Třída Type II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm a S960m
- **Razítko**: Definuje interní velikost nasazení od Microsoftu velkých instancí HANA. Než bude možné nasazovat velké jednotky instancí HANA, musí být v umístění datového centra nasazené razítko pro velké instance, které se skládá z výpočetních, síťových a úložných stojanů. Toto nasazení se nazývá razítko velkého výskytu instance HANA nebo revize 4 (viz níže) na základě použití alternativního **řádku velkých instancí** .
- **Revize**: K dispozici jsou dvě různé revize razítek pro velká instance v HANA. Liší se v architektuře a blízkosti hostitelů virtuálních počítačů Azure.
    - "Revize 3" (rev 3): je původní návrh, který byl nasazen ze polovině roku 2016
    - "Revize 4" (Rev 4): je nový návrh, který může poskytnout užší blízkost hostitelům virtuálních počítačů Azure a tím snížit latenci sítě mezi virtuálními počítači Azure a jednotkami velkých instancí HANA. 

K dispozici je celá řada dalších prostředků pro nasazení úlohy SAP v cloudu. Pokud máte v úmyslu spustit nasazení SAP HANA v Azure, musíte mít zkušenosti s principy Azure IaaS a nasazením úloh SAP na Azure IaaS. Než budete pokračovat, přečtěte si téma [použití řešení SAP na virtuálních počítačích Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , kde najdete další informace. 

**Další postup**
- Reference k [certifikaci HLI](hana-certification.md)
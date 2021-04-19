---
title: Poznejte SAP HANA v Azure (velké instance) | Microsoft Docs
description: Seznamte se s podmínkami SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 4/16/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a243b348c01e6d1297a6a1fe016e3b6bc8d98d47
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719074"
---
# <a name="know-the-terms"></a>Seznámení s podmínkami

V průvodci architekturou a technickým nasazením se často používá několik běžných definic. Všimněte si následujících podmínek a jejich významu:

- **IaaS**: infrastruktura jako služba.
- **PaaS**: platforma jako služba.
- **SaaS**: software jako služba.
- **Součást SAP**: samostatná aplikace SAP, jako je například součást ERP Central (ECC), Business Warehouse (černobílý), správce řešení nebo Enterprise Portal (EP). Komponenty SAP můžou být založené na tradičních technologiích ABAP a Java nebo na NetWeaver aplikaci, jako jsou třeba obchodní objekty.
- **Prostředí SAP**: jedna nebo víc komponent SAP se logicky seskupují k provádění obchodních funkcí, jako je vývoj, zabezpečování kvality, školení, zotavení po havárii nebo produkce.
- **SAP na šířku**: odkazuje na celé prostředky SAP na svém IT oddělení. SAP na šířku zahrnuje všechna produkční a neprodukční prostředí.
- **Systém SAP**: kombinace vrstvy a aplikační vrstvy systému DBMS, například vývojového systému SAP ERP, SAP BW testovacího systému a produkčního systému SAP CRM. Nasazení Azure nepodporuje dělení těchto dvou vrstev mezi místními a Azure. Systém SAP je buď nasazený místně, nebo je nasazený v Azure. Můžete nasadit různé systémy SAP na šířku do Azure nebo do místního prostředí. Můžete například nasadit vývojové a testovací systémy SAP CRM do Azure, zatímco nasazujete produkční systém SAP CRM v místním prostředí. V případě SAP HANA v Azure (velké instance) je zamýšlená možnost hostování aplikace SAP pro systémy SAP na virtuálních počítačích a související instanci SAP HANA na jednotce v SAP HANA na platformě Azure (velké instance).
- **Razítko s velkými instancemi**: sada hardwarových infrastruktur, která je SAP HANA s certifikací TDI a je vyhrazená ke spouštění SAP HANA instancí v rámci Azure.
- **SAP HANA v Azure (velké instance):** Oficiální název nabídky v Azure, ve kterém se spouštějí instance HANA v SAP HANA hardwaru TDI, který je nasazený ve velkých názvech instancí v různých oblastech Azure. Související *Velká instance Hana* je krátká pro *SAP HANA v Azure (velké instance)* a v této technické příručce pro nasazení se používá často.
- **Mezi různými** místy: popisuje situaci, kdy se virtuální počítače nasazují do předplatného Azure, které má připojení typu Site-to-site, Multi-Site nebo Azure ExpressRoute mezi místními datovými centry a Azure. V běžné dokumentaci k Azure jsou tyto typy nasazení popsány také jako mezi místními scénáři. Důvodem připojení je rozšiřování místních domén, místních Azure Active Directory/OpenLDAP a místní DNS do Azure. Místní orientace se rozšiřuje na prostředky Azure předplatných Azure. S tímto rozšířením můžou být virtuální počítače součástí místní domény. 

   Uživatelé domény v místní doméně mají přístup k serverům a spouštějí služby na těchto virtuálních počítačích (například služby DBMS). Je možné komunikovat a překlad názvů mezi virtuálními počítači nasazenými místně a virtuálními počítači nasazenými v Azure. Tento scénář je typický pro způsob, jakým se nasazují většina prostředků SAP. Další informace najdete v tématu [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) a [vytvoření virtuální sítě s připojením typu Site-to-site pomocí Azure Portal](../../../vpn-gateway/tutorial-site-to-site-portal.md).
- **Tenant**: zákazník nasazený v aplikaci Hana razítko s velkými instancemi se dostane do tenanta jako izolované *.* Tenant je izolovaný v síti, úložišti a výpočetní vrstvě z jiných tenantů. Úložiště a výpočetní jednotky přiřazené různým klientům se nemůžou vzájemně zobrazit ani vzájemně komunikovat na úrovni razítka velké instance služby HANA. Zákazník se může rozhodnout, že mají nasazení v různých klientech. I potom neexistuje žádná komunikace mezi klienty na úrovni razítka velké instance HANA.
- **Kategorie SKU**: u velké instance Hana jsou nabízeny následující dvě kategorie SKU:
    - **Třída Type I**: S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 a S224m
    - **Type II – třída**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm a S960m
- **Razítko**: definuje interní velikost nasazení od Microsoftu velkých instancí Hana. Než bude možné nasazovat velké jednotky instancí HANA, musí být v umístění datového centra nasazené razítko pro velké instance, které se skládá z výpočetních, síťových a úložných stojanů. Toto nasazení se nazývá razítko velkého výskytu instance HANA nebo revize 4 (viz níže) na základě použití alternativního **řádku velkých instancí** .
- **Revize**: Existují dvě různé revize razítka pro velká instance pro všechna razítka Hana. Ty se liší v architektuře a blízkosti hostitelů virtuálních počítačů Azure.
    - "Revize 3" (rev 3) je původní návrh nasazený od středu 2016.
    - "Revize 4,2" (Rev 4,2) je nový návrh, který poskytuje užší blízkost pro hostitele virtuálních počítačů Azure. Rev 4,2 nabízí extrémně nízkou latenci sítě mezi virtuálními počítači Azure a jednotkami velkých instancí HANA. Prostředky v Azure Portal se označují jako BareMetal infrastruktura. Zákazníci mají přístup ke svým prostředkům jako BareMetal instance z Azure Portal. 

K dispozici je celá řada dalších prostředků pro nasazení úlohy SAP v cloudu. Pokud máte v úmyslu spustit nasazení SAP HANA v Azure, musíte mít zkušenosti s principy Azure IaaS a nasazením úloh SAP na Azure IaaS. Než budete pokračovat, přečtěte si téma [použití řešení SAP na virtuálních počítačích Azure](get-started.md) , kde najdete další informace. 

## <a name="next-steps"></a>Další kroky
- Přečtěte si [HLI certifikace](hana-certification.md).
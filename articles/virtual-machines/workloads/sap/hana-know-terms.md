---
title: Vědět podmínky SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Vědět podmínky SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a8131bc953c2aba3c8d33f866cbbe9b1e232e168
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61481033"
---
# <a name="know-the-terms"></a>Seznámení s podmínkami

Několik běžných definice se běžně používají v architektury a technické příručce pro nasazení. Mějte na paměti následující termíny a jejich význam:

- **IaaS**: Infrastruktura jako služba.
- **PaaS**: Platforma jako služba.
- **SaaS**: Software jako služba.
- **Komponenta SAP**: Jednotlivé SAP aplikace, jako je ERP ústřední součást (ECC), Business Warehouse (BW), správci řešení nebo Enterprise Portal (RP). Komponenty SAP může být založen na tradičních technologií ABAP a Java nebo jiných NetWeaver na základě aplikace, například pro obchodní objekty.
- **Prostředí SAP**: Jeden nebo více komponent SAP logicky seskupeny provádět obchodní funkce, jako je vývoj, kontrola kvality, školení, zotavení po havárii nebo produkčního prostředí.
- **Prostředí SAP**: Odkazuje na celý prostředky SAP v prostředí vašeho IT. Prostředí SAP zahrnuje všechny produkčního prostředí a neprodukční prostředí.
- **Systém SAP**: Kombinace DBMS vrstvy a aplikaci vrstvu, třeba vývoj systému SAP ERP, SAP BW otestovat systém a produkční systém SAP CRM. Nasazení v Azure nepodporují dělení tyto dvě vrstvy mezi místními a Azure. Systém SAP je nasazená místně nebo je nasazené v Azure. Můžete nasadit různé systémy prostředí SAP do Azure nebo místně. Například můžete nasadit vývoj SAP CRM a systémů v Azure můžete testovat při nasazování SAP CRM produkční systém místní. Pro SAP HANA v Azure (velké instance) je určena hostujete aplikační vrstvě SAP systémů SAP na virtuálních počítačích a související instance SAP HANA na jednotce v SAP HANA v Azure (velké instance) razítko.
- **Velké Instance razítka**: Zásobník hardware infrastruktury, který je certifikací pro SAP HANA TDI a vyhrazených ke spuštění instance systému SAP HANA v Azure.
- **SAP HANA v Azure (velké instance):** Oficiální název nabídky v Azure ke spuštění instance HANA v certifikací pro SAP HANA TDI hardware, který je nasazený v velká Instance razítka v různých oblastech Azure. Související výraz *velká Instance HANA* je zkratka pro *SAP HANA v Azure (velké instance)* a se běžně používá v této příručce technického nasazení.
- **Mezi různými místy**: Popisuje scénář, ve které jsou nasazené virtuální počítače s předplatným Azure, který má připojení Azure ExpressRoute mezi místní datová centra a Azure, site-to-site a multi-Site. Dokumentace v běžných Azure a tyto druhy nasazení jsou také popsány jako scénáře mezi různými místy. Důvod pro připojení je k rozšíření místních domén, místní Azure Active Directory/OpenLDAP a místní DNS do Azure. V místním prostředí je rozšířit na prostředky Azure z předplatných Azure. U tohoto rozšíření virtuální počítač může být součástí místní domény. 

   Domény uživatele z místní domény můžete přístup k serverům a spustit služby na těchto virtuálních počítačů (například systému DBMS služby). Místně nasadili komunikace a název řešení mezi virtuálními počítači a virtuálních počítačů nasazených Azure je možné. Tento scénář je typický pro způsob, ve které jsou nasazené prostředky většina SAP. Další informace najdete v tématu [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a [vytvoření virtuální sítě s připojením site-to-site pomocí webu Azure portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Tenant**: Získá zákazník nasazené v razítku velká Instance HANA samostatný do *tenanta.* Tenant je izolovaný v oblasti sítí, úložiště a výpočetní vrstvy od ostatních tenantů. Úložiště a výpočetní jednotky přiřazená různých tenantech nelze zobrazit mezi sebou ani vzájemně komunikovat na úrovni razítko velká Instance HANA. Zákazník se může rozhodnout pro nasazení do různých tenantech. Dokonce i pak neexistuje žádná komunikace mezi klienty na úrovni razítko velká Instance HANA.
- **Kategorie SKU**: Pro velká Instance HANA nabízíme následující dvě kategorie skladové položky:
    - **Typ třídy I**: S72 S72m, S96, S144, S144m, S192, S192m a S192xm
    - **Zadejte třídu II**: S384 S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm a S960m


Různé další zdroje informací jsou k dispozici pro nasazení úloh SAP v cloudu. Pokud budete chtít provést nasazení SAP Hana v Azure, musíte být zkušenosti s a nezná zásady Azure IaaS a nasazení úloh SAP v Azure IaaS. Než budete pokračovat, naleznete v tématu [řešení použití SAP na virtuálních počítačích Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Další informace. 

**Další postup**
- Přečtěte si [HLI certifikace](hana-certification.md)
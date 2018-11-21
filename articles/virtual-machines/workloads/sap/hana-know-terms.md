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
ms.openlocfilehash: 902c4c94cc7366af342816e9b4776af603711c8b
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275432"
---
# <a name="know-the-terms"></a>Vědět podmínky

Několik běžných definice se běžně používají v architektury a technické příručce pro nasazení. Mějte na paměti následující termíny a jejich význam:

- **IaaS**: infrastruktura jako služba.
- **PaaS**: platforma jako služba.
- **SaaS**: Software jako služba.
- **Komponenta SAP**: jednotlivé aplikace SAP, jako je ERP ústřední součást (ECC), Business Warehouse (BW), správci řešení nebo Enterprise Portal (RP). Komponenty SAP může být založen na tradičních technologií ABAP a Java nebo jiných NetWeaver na základě aplikace, například pro obchodní objekty.
- **Prostředí SAP**: jedna nebo více součástí SAP logicky seskupeny provádět obchodní funkce, jako je vývoj, kontrola kvality, školení, zotavení po havárii nebo produkčního prostředí.
- **Prostředí SAP**: odkazuje na celý prostředky SAP v prostředí vašeho IT. Prostředí SAP zahrnuje všechny produkčního prostředí a neprodukční prostředí.
- **Systém SAP**: kombinace DBMS vrstvy a aplikační vrstvě například vývoj systému SAP ERP, systém SAP BW testovací a produkční systém SAP CRM. Nasazení v Azure nepodporují dělení tyto dvě vrstvy mezi místními a Azure. Systém SAP je nasazená místně nebo je nasazené v Azure. Můžete nasadit různé systémy prostředí SAP do Azure nebo místně. Například můžete nasadit vývoj SAP CRM a systémů v Azure můžete testovat při nasazování SAP CRM produkční systém místní. Pro SAP HANA v Azure (velké instance) je určena hostujete aplikační vrstvě SAP systémů SAP na virtuálních počítačích a související instance SAP HANA na jednotce v SAP HANA v Azure (velké instance) razítko.
- **Velké Instance razítka**: zásobníku hardwarové infrastruktury, který je certifikací pro SAP HANA TDI a vyhrazených ke spuštění instance systému SAP HANA v Azure.
- **SAP HANA v Azure (velké instance):** oficiální název nabídky v Azure ke spuštění HANA instance na certifikací pro SAP HANA TDI hardwaru, který je nasazený v velká Instance razítka v různých oblastech Azure. Související výraz *velká Instance HANA* je zkratka pro *SAP HANA v Azure (velké instance)* a se běžně používá v této příručce technického nasazení.
- **Mezi různými místy**: popisuje scénář, ve které jsou nasazené virtuální počítače s předplatným Azure, který má připojení Azure ExpressRoute mezi místní datová centra a Azure, site-to-site a multi-Site. Dokumentace v běžných Azure a tyto druhy nasazení jsou také popsány jako scénáře mezi různými místy. Důvod pro připojení je k rozšíření místních domén, místní Azure Active Directory/OpenLDAP a místní DNS do Azure. V místním prostředí je rozšířit na prostředky Azure z předplatných Azure. U tohoto rozšíření virtuální počítač může být součástí místní domény. 

   Domény uživatele z místní domény můžete přístup k serverům a spustit služby na těchto virtuálních počítačů (například systému DBMS služby). Místně nasadili komunikace a název řešení mezi virtuálními počítači a virtuálních počítačů nasazených Azure je možné. Tento scénář je typický pro způsob, ve které jsou nasazené prostředky většina SAP. Další informace najdete v tématu [plánování a návrh pro Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a [vytvoření virtuální sítě s připojením site-to-site pomocí webu Azure portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Tenant**: zákazník nasazené v razítku velká Instance HANA načte samostatný do *tenanta.* Tenant je izolovaný v oblasti sítí, úložiště a výpočetní vrstvy od ostatních tenantů. Úložiště a výpočetní jednotky přiřazená různých tenantech nelze zobrazit mezi sebou ani vzájemně komunikovat na úrovni razítko velká Instance HANA. Zákazník se může rozhodnout pro nasazení do různých tenantech. Dokonce i pak neexistuje žádná komunikace mezi klienty na úrovni razítko velká Instance HANA.
- **Kategorie SKU**: pro velké Instance HANA, jsou k dispozici tyto dvě kategorie skladové položky:
    - **Typ třídy I**: S72 S72m, S96, S144, S144m, S192, S192m a S192xm
    - **Zadejte třídu II**: S384 S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm a S960m


Různé další zdroje informací jsou k dispozici pro nasazení úloh SAP v cloudu. Pokud budete chtít provést nasazení SAP Hana v Azure, musíte být zkušenosti s a nezná zásady Azure IaaS a nasazení úloh SAP v Azure IaaS. Než budete pokračovat, naleznete v tématu [řešení použití SAP na virtuálních počítačích Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Další informace. 

**Další kroky**
- Přečtěte si [HLI certifikace](hana-certification.md)
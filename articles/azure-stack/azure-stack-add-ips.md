---
title: Přidání veřejné IP adresy ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak přidat další veřejné IP adresy do služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: scottnap
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: ed2205455c4d3c82554f3d552bacc059fffa68e7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174652"
---
# <a name="add-public-ip-addresses"></a>Přidání veřejné IP adresy
*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*  

Zjistěte, jak přidat další veřejné IP adresy do služby Azure Stack.  V tomto článku budeme odkazovat na externí adresy jako veřejné IP adresy, ale ve službě Azure Stack, ten je určený k odkazování na přidání bloky IP adres k externí síti.  Určuje, zda tuto externí síť je veřejný Internet směrovatelné nebo je na intranetu a využívá privátní adresní prostor nezáleží pro účely tohoto článku.  Kroky jsou stejné. 

## <a name="add-a-public-ip-address-pool"></a>Přidání fondu adres veřejné IP adresy
Veřejné IP adresy můžete přidat do systému Azure Stack kdykoli po počátečním nasazení v systému Azure Stack. Podívejte se na tom, jak [spotřeby zobrazení veřejných IP adres](azure-stack-viewing-public-ip-address-consumption.md) zobrazíte jaké aktuální využití a veřejnou IP adresu dostupnosti je ve vaší službě Azure Stack.

Na vysoké úrovni proces přidávání nového bloku veřejnou IP adresu do služby Azure Stack vypadá například takto:

 ![Přidání toku protokolu IP](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>Získat blok adres od svého poskytovatele
První věc, kterou je potřeba udělat, je získat blok adres, které chcete přidat do služby Azure Stack.  V závislosti na tom, kde získat vaše blok adres z bude nutné vzít v úvahu co dobu realizace je a spravovat proti rychlost, jakou jsou spotřeby veřejných IP adres ve službě Azure Stack.  

> [!IMPORTANT]
> Azure Stack bude přijímat všechny blok adres, které poskytujete, tak dlouho, dokud je platná adresa blok a se nepřekrývá s existující rozsahu adres ve službě Azure Stack.  Ujistěte se prosím, že získat platnou adresu blok, který je směrovatelné a které se nepřekrývají s externí sítě, ke kterému je připojený Azure Stack.  Jakmile přidáte rozsahu do služby Azure Stack, nelze odebrat.

## <a name="add-the-ip-address-range-to-azure-stack"></a>Přidat rozsah IP adres do služby Azure Stack

1. V internetovém prohlížeči přejděte na řídicí panel portálu správce.  V tomto příkladu použijeme https://adminportal.local.azurestack.external.  
2.  Přihlaste se k portálu pro správu služby Azure Stack jako operátor cloudu.
3.  Na výchozí řídicí panel – seznam pro správu oblasti vyhledejte a vyberte oblast, kterou chcete spravovat, v tomto příkladu místní.
4.  Vyhledejte poskytovatele prostředků dlaždici a klikněte na poskytovatel síťových prostředků.
5.  Klikněte na veřejnou IP adresu fondu dlaždici využití.
6.  Klikněte na tlačítko Přidat IP adresu fondu.
7.  Zadejte název fondu IP adres.  Název zvoleném je to umožní snadno identifikovat fond IP adres, můžete ji volat libovolně.  Je dobrým zvykem, aby název stejné jako rozsah adres, ale, který není povinné.
8.   Zadejte blok adres, které chcete přidat v zápisu CIDR.  Příklad: 192.168.203.0/24
9.  Když zadáte platný rozsah CIDR v poli (blok CIDR) rozsah adres počáteční IP adresa, koncová IP adresa a k dispozici IP adresy pole budou automaticky vyplněna.  Jsou jen pro čtení a automaticky vygenerovat tak, že nebudete moct změnit tyto beze změny hodnotu v poli rozsah adres.
10. Po zkontrolování informací v okně a potvrdí všechno, co vypadá opravit, klikněte na tlačítko Ok potvrďte změnu a přidat rozsah adres do služby Azure Stack.

## <a name="update-the-acls-on-your-top-of-rack-switches"></a>Aktualizovat seznamy ACL v přepínačích Top-of-Rack
Poslední věcí, kterou je třeba provést, chcete-li povolit nově přidané rozsah IP adres práce je aktualizovat seznamů řízení přístupu (ACL) v přepínačích Top-of-Rack (ToR).  Seznamy ACL v tak, že připojení mimo Azure Stack na nově přidaných rozsah IP adres nebudou fungovat až do nového rozsahu jsou uzamčené přepínače ToR je přidán do seznamů řízení přístupu na přepínači.  

Budete muset kontaktovat výrobce OEM a pracovat s nimi se aktualizovat seznam ACL v přepínači ToR.  Mají nástroje potřebné k tomu podporované způsobem.


## <a name="next-steps"></a>Další postup 
[Kontrola škálovací jednotku uzlu akce](azure-stack-node-actions.md) 

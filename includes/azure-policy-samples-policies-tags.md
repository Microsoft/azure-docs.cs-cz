---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 5a9d60cd019e044fd19a8670c0843a8d155f5433
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166612"
---
|Název |Popis |Vliv (s) |Verze |
|---|---|---|---|
|[Přidání značky do skupin prostředků](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddTag_ResourceGroup_Modify.json) |Přidá zadanou značku a hodnotu, pokud u kterékoli skupiny prostředků chybí Tato značka, která je vytvořena nebo aktualizována. Stávající skupiny prostředků je možné opravit aktivací úlohy nápravy. Pokud značka existuje s jinou hodnotou, nebude změněna. |Upravíte |1.0.0 |
|[Přidání značky k prostředkům](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddTag_Modify.json) |Přidá zadanou značku a hodnotu, pokud některý z prostředků chybějící tato značka vytvoří nebo aktualizuje. Stávající prostředky je možné napravit aktivací úlohy nápravy. Pokud značka existuje s jinou hodnotou, nebude změněna. Značky ve skupinách prostředků se neupravují. |Upravíte |1.0.0 |
|[Přidání nebo nahrazení značky ve skupinách prostředků](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddOrReplaceTag_ResourceGroup_Modify.json) |Při vytvoření nebo aktualizaci libovolné skupiny prostředků přidá nebo nahradí určenou značku a hodnotu. Stávající skupiny prostředků je možné opravit aktivací úlohy nápravy. |Upravíte |1.0.0 |
|[Přidání nebo nahrazení značky u prostředků](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddOrReplaceTag_Modify.json) |Při vytvoření nebo aktualizaci libovolného prostředku přidá nebo nahradí určenou značku a hodnotu. Stávající prostředky je možné napravit aktivací úlohy nápravy. Značky ve skupinách prostředků se neupravují. |Upravíte |1.0.0 |
|[Připojit značku a její výchozí hodnotu](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ApplyTag_Append.json) |Připojí zadanou značku a hodnotu, pokud se v jakémkoli prostředku, ve kterém chybí Tato značka, vytvoří nebo aktualizuje. Neupravuje značky prostředků vytvořených před použitím této zásady, dokud se tyto prostředky nezmění. Neplatí pro skupiny prostředků. K dispozici jsou nové zásady efektu úpravy, které podporují nápravu značek u stávajících prostředků (viz https://aka.ms/modifydoc). |příloh |1.0.0 |
|[Přidat značku a její výchozí hodnotu do skupin prostředků](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupApplyTag_Append.json) |Připojí zadanou značku a hodnotu, pokud je vytvořena nebo aktualizována kterákoli ze skupin prostředků, ve které chybí Tato značka. Neupravuje značky skupin prostředků vytvořených před použitím této zásady, dokud se tyto skupiny prostředků nezmění. K dispozici jsou nové zásady efektu úpravy, které podporují nápravu značek u stávajících prostředků (viz https://aka.ms/modifydoc). |příloh |1.0.0 |
|[Připojit značku a její hodnotu ze skupiny prostředků](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_Append.json) |Připojí určenou značku ke své hodnotě ze skupiny prostředků, pokud některý z prostředků, u kterých chybí Tato značka, je vytvořen nebo aktualizován. Neupravuje značky prostředků vytvořených před použitím této zásady, dokud se tyto prostředky nezmění. K dispozici jsou nové zásady efektu úpravy, které podporují nápravu značek u stávajících prostředků (viz https://aka.ms/modifydoc). |příloh |1.0.0 |
|[Zdědit značku ze skupiny prostředků](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_AddOrReplace_Modify.json) |Přidá nebo nahradí určenou značku a hodnotu z nadřazené skupiny prostředků při vytvoření nebo aktualizaci libovolného prostředku. Stávající prostředky je možné napravit aktivací úlohy nápravy. |Upravíte |1.0.0 |
|[Zdědit značku ze skupiny prostředků, pokud chybí](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_Add_Modify.json) |Přidá zadanou značku s její hodnotou z nadřazené skupiny prostředků v případě, že u libovolného prostředku chybí Tato značka nebo je vytvořena nebo aktualizována. Stávající prostředky je možné napravit aktivací úlohy nápravy. Pokud značka existuje s jinou hodnotou, nebude změněna. |Upravíte |1.0.0 |
|[Vyžadovat určenou značku](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/RequireTag_Deny.json) |Vynutil existenci značky. Neplatí pro skupiny prostředků. |odmítnout |1.0.0 |
|[Vyžadovat určenou značku u skupin prostředků](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupRequireTag_Deny.json) |Vynutilo existenci značky ve skupinách prostředků. |odmítnout |1.0.0 |
|[Vyžadovat značku a její hodnotu](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/RequireTagAndValue_Deny.json) |Vynutí požadovanou značku a její hodnotu. Neplatí pro skupiny prostředků. |odmítnout |1.0.0 |
|[Vyžadovat značku a její hodnotu ve skupinách prostředků](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupRequireTagAndValue_Deny.json) |Vynutila povinnou značku a její hodnotu ve skupinách prostředků. |odmítnout |1.0.0 |

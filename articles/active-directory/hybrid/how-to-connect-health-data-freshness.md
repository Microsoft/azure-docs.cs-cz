---
title: Azure AD Connect Health – data služby Health service není datum upozornění | Dokumentace Microsoftu
description: Tento dokument popisuje příčinu výstrahy "data služby Health service není aktuální." a jak řešit potíže se.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 430ea5f0a6f737d7632a4352c24d893368b80558
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46315095"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Data služby Health service není aktuální výstrahy

## <a name="overview"></a>Přehled
<li>Azure AD Connect Health vyvolá data novou výstrahu, jestliže neobdrží všechny datové body ze serveru pro dvě hodiny. Název upozornění je **data služby Health service není aktuální**. </li>
<li>**Upozornění** výstraha o stavu je vyvoláno, pokud Connect Health přijme prvky částečná data ze serveru odesílají dvě hodiny. Stav upozornění neaktivuje e-mailová oznámení pro správce tenanta. </li>
<li>**Chyba** stav upozornění je vyvoláno, pokud Connect Health přijme všechny datové prvky odeslaných ze serveru pro dvě hodiny. Chyba stavu aktivuje upozornění e-mailová oznámení správce tenanta. </li>

>[!IMPORTANT] 
> Toto oznámení následuje Connect Health [zásady uchovávání dat](reference-connect-health-user-privacy.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>Postup při řešení potíží 
* Ujistěte se, že se přenášejí prostřednictvím a vyhovět [části věnované požadavkům](how-to-connect-health-agent-install.md#requirements).
* Použití [nástroj pro testování připojení](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) ke zjišťování problémů s připojením.


## <a name="next-steps"></a>Další postup
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)

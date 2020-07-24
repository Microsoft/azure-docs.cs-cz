---
title: Úložiště dat identity pro australské zákazníky – Azure AD
description: Přečtěte si, kde Azure Active Directory ukládá data týkající se identit pro své australské zákazníky.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/21/2020
ms.custom: it-pro, seodec18, references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 749ee479f19b0855700cf1ca15aefd9b983dd7f7
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87129538"
---
# <a name="customer-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Úložiště zákaznických dat pro australské zákazníky Austrálie a Nového Zélandu v Azure Active Directory

Azure Active Directory (Azure AD) ukládá svá zákaznická data v geografickém umístění na základě země, kterou jste zadali při registraci služby Microsoft Online Services. Online služby Microsoftu zahrnují Office 365 a Azure. 

Informace o tom, kde se nachází Azure AD a další data služeb Microsoftu, najdete v části [kde se nacházejí vaše data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v centru zabezpečení Microsoftu.

Od 26. února 2020 společnost Microsoft zahájila ukládání zákaznických dat služby Azure AD pro nové klienty pomocí fakturační adresy Austrálie nebo Nového Zélandu v rámci australského datacentra. Od 1. května 2020 do 31. října 2020 Microsoft migruje stávající klienty, kteří mají fakturační adresu Austrálie nebo Nového Zélandu, do australského datacentra, aniž by to vyžadovalo akci zákazníka. Proces migrace nezahrnuje žádné výpadky pro zákazníky a nebude mít vliv na funkčnost tenanta během migrace.

Kromě toho některé funkce služby Azure AD ještě nepodporují ukládání zákaznických dat v Austrálii. Konkrétní informace o funkcích najdete v [mapě dat služby Azure AD](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9). Například Microsoft Azure Multi-Factor Authentication ukládá zákaznická data v USA a globálně je zpracovává. Podívejte [se na data o zaregistrování dat a zákaznických datech pro Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency).

> [!NOTE]
> Produkty, služby a aplikace třetích stran, které se integrují se službou Azure AD, mají přístup k zákaznickým datům. Vyhodnoťte jednotlivé produkty, služby a aplikace, které používáte k určení toho, jak se budou zákaznická data zpracovávat pomocí konkrétního produktu, služby a aplikace, a jestli splňují požadavky na úložiště dat vaší společnosti. Další informace o rezidenci dat služeb Microsoftu najdete v části [Jaké je umístění vašich dat?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v Centru zabezpečení Microsoftu.


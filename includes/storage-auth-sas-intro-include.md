---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3af8077627d56ce44c5e2959e2c79b967b09d9e5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011941"
---
Sdílený přístupový podpis (SAS) umožňuje udělit omezený přístup k kontejnerům a objektům blob ve vašem účtu úložiště. Při vytváření SAS zadáte omezení, včetně toho, ke kterým Azure Storage prostředkům má klient povolený přístup, jaká oprávnění jsou v těchto prostředcích a jak dlouho je SAS platný.

Každé přidružení zabezpečení je podepsáno klíčem. SAS můžete podepsat jedním ze dvou způsobů:

- Pomocí klíče vytvořeného pomocí pověření Azure Active Directory (Azure AD). Podpis SAS podepsaný pomocí přihlašovacích údajů služby Azure AD je SAS pro *delegování uživatelů* .
- Pomocí klíče účtu úložiště. Podpis *SAS služby* i *účtu SAS* jsou podepsané klíčem účtu úložiště.

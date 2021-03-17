---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75371768"
---
Sdílený přístupový podpis (SAS) umožňuje udělit omezený přístup k kontejnerům a objektům blob ve vašem účtu úložiště. Při vytváření SAS zadáte omezení, včetně toho, ke kterým Azure Storage prostředkům má klient povolený přístup, jaká oprávnění jsou v těchto prostředcích a jak dlouho je SAS platný.

Každé přidružení zabezpečení je podepsáno klíčem. SAS můžete podepsat jedním ze dvou způsobů:

- Pomocí klíče vytvořeného pomocí pověření Azure Active Directory (Azure AD). Podpis SAS podepsaný pomocí přihlašovacích údajů služby Azure AD je SAS pro *delegování uživatelů* .
- Pomocí klíče účtu úložiště. Podpis *SAS služby* i *účtu SAS* jsou podepsané klíčem účtu úložiště.

SAS delegování uživatelů nabízí nadřazené zabezpečení SAS, které je podepsáno klíčem účtu úložiště. Pokud je to možné, Microsoft doporučuje použít SAS delegování uživatele. Další informace najdete v tématu [udělení omezeného přístupu k datům pomocí sdílených přístupových podpisů (SAS)](../articles/storage/common/storage-sas-overview.md).

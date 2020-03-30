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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75371768"
---
Sdílený přístupový podpis (SAS) umožňuje udělit omezený přístup k kontejnerům a objektům BLOB v účtu úložiště. Při vytváření SAS zadáte jeho omezení, včetně prostředků Azure Storage, ke kterým má klient přístup, jaká oprávnění mají k těmto prostředkům a jak dlouho je SAS platný.

Každý SAS je podepsán klíčem. SAS můžete podepsat jedním ze dvou způsobů:

- S klíčem vytvořeným pomocí přihlašovacích údajů služby Azure Active Directory (Azure AD). SAS, který je podepsán přihlašovacími údaji Azure AD je *uživatelské delegování* SAS.
- S klíčem účtu úložiště. Služba *SAS* i *účet SAS* jsou podepsány klíčem účtu úložiště.

Uživatelské delegování SAS nabízí vynikající zabezpečení SAS, který je podepsán klíčem účtu úložiště. Společnost Microsoft doporučuje používat uživatelské delegování SAS, pokud je to možné. Další informace naleznete v tématu [Udělení omezeného přístupu k datům se sdílenými přístupovými podpisy (SAS).](../articles/storage/common/storage-sas-overview.md)

---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: b0be074c995fcc62f63a4a2ebf4149d3040e7b6b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750437"
---
## <a name="best-practices"></a>Osvědčené postupy

V této části najdete některé doporučené postupy pro rekurzivní nastavení seznamů ACL. 

#### <a name="handling-runtime-errors"></a>Zpracování chyb běhového prostředí

K běhové chybě může dojít z mnoha důvodů (například při výpadku nebo potížích s připojením klienta). Pokud dojde k chybě za běhu, restartujte rekurzivní proces seznamu ACL. Seznamy řízení přístupu (ACL) lze znovu použít na položky, aniž by došlo k negativnímu dopadu. 

#### <a name="handling-permission-errors-403"></a>Zpracování chyb oprávnění (403)

Pokud při spuštění rekurzivního procesu seznamu ACL narazíte na výjimku řízení přístupu, váš [objekt zabezpečení](../articles/role-based-access-control/overview.md#security-principal) služby AD nemusí mít dostatečná oprávnění k použití seznamu ACL pro jednu nebo více podřízených položek v hierarchii adresáře. Pokud dojde k chybě oprávnění, proces se zastaví a poskytne se token pro pokračování. Opravte problém s oprávněním a pak použijte token pro pokračování ke zpracování zbývající datové sady. Adresáře a soubory, které již byly úspěšně zpracovány, nemusí být zpracovány znovu. Můžete také zvolit restartování rekurzivního procesu seznamu ACL. Seznamy řízení přístupu (ACL) lze znovu použít na položky, aniž by došlo k negativnímu dopadu. 

#### <a name="credentials"></a>Přihlašovací údaje 

Doporučujeme zřídit objekt zabezpečení služby Azure AD, kterému byla přiřazena role [vlastníka dat objektů BLOB úložiště](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) v oboru cílového účtu úložiště nebo kontejneru. 

#### <a name="performance"></a>Výkon 

Pokud chcete snížit latenci, doporučujeme spustit rekurzivní proces seznamu ACL na virtuálním počítači Azure, který je umístěný ve stejné oblasti jako váš účet úložiště. 

#### <a name="acl-limits"></a>Omezení seznamu ACL

Maximální počet seznamů řízení přístupu, které můžete použít u adresáře nebo souboru, je 32 seznamů ACL a 32 výchozích seznamů ACL. Další informace najdete v tématu [Řízení přístupu ve službě Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 0bc5977a581006dc760c0435f9d68371ced7e4cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648775"
---
Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Další informace najdete v tématu [Azure Storage šifrování pro](../articles/storage/common/storage-service-encryption.md)neaktivní neaktivní data.

Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete zadat klíče spravované zákazníkem, které se použijí k šifrování dat objektů BLOB a souborů. Aby funkce mohly získat přístup k účtu úložiště, musí se tyto klíče vyskytovat v Azure Key Vault. Další informace najdete v části [šifrování v klidovém formátu pomocí klíčů spravovaných zákazníkem](../articles/azure-functions/configure-encrypt-at-rest-using-cmk.md).  
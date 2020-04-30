---
title: Ověření runbooků Azure Automation pomocí Amazon Web Services
description: Tento článek popisuje postup vytvoření a ověření přihlašovacích údajů Amazon Web Services (AWS) pro runbooky ve službě Azure Automation, které spravují prostředky AWS.
keywords: ověřování aws, konfigurace aws
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 1bf60d17af1d9866de6a62ac538fa0bd9a15ce52
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82113387"
---
# <a name="authenticate-azure-automation-runbooks-with-amazon-web-services"></a>Ověření runbooků Azure Automation pomocí Amazon Web Services

Automatizaci běžných úkolů pomocí prostředků ve službě Amazon Web Services můžete provést pomocí runbooků Automation v Azure. V AWS můžete automatizovat celou řadu úloh pomocí runbooků Automation, stejně to děláte s prostředky v Azure. Potřebujete jen dvě věci:

* Předplatné AWS a sadu přihlašovacích údajů. Zejména přístupový klíč a tajný klíč AWS. Další informace najdete v článku [Použití přihlašovacích údajů AWS](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Předplatné Azure a účet Automation.

Abyste mohli ověřovat pomocí AWS, musíte zadat sadu přihlašovacích údajů AWS a ověřit svoje runbooky spuštěné ve službě Azure Automation. Pokud už máte vytvořený účet Automation a chcete ho použít k ověřování pomocí AWS, postupujte podle návodu v následující části. Pokud chcete vyhradit účet pro Runbooky cílící na prostředky AWS, měli byste nejdřív vytvořit nový [účet Automation](automation-create-standalone-account.md) a přeskočit tento krok a vytvořit účet Spustit jako. Po vytvoření účtu postupujte podle následujících kroků a dokončete konfiguraci.

## <a name="configure-automation-account"></a>Konfigurace účtu Automation

Pokud má Azure Automation komunikovat s AWS, musíte nejdřív načíst přihlašovací údaje AWS a uložit je jako assety ve službě Azure Automation. Proveďte následující kroky, které jsou popsány v dokumentu AWS [Správa přístupových klíčů k vašemu účtu AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html), vytvořte přístupový klíč a zkopírujte **Access Key ID** (ID přístupového klíče) a **Secret Access Key** (Tajný přístupový klíč) (případně si můžete soubor s klíčem stáhnout a uložit na bezpečné místo).

Po vytvoření a zkopírování bezpečnostních klíčů AWS vytvořte pomocí účtu Azure Automation asset přihlašovacích údajů, bezpečně klíče uložte a odkazujte na ně ve svých runboocích. Postupujte podle kroků v části: **vytvoření nových přihlašovacích údajů** v [prostředcích přihlašovacích údajů v Azure Automation](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal) článku a zadejte následující informace:

1. Do pole **Název** zadejte **AWScred** nebo odpovídající hodnotu v souladu se svými standardy pro vytváření názvů.
2. Do pole **uživatelské jméno** zadejte své **ID přístupu** a **klíč tajného přístupu** do pole **heslo** a **potvrzení hesla** .

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření runbooků pro automatizaci úloh v AWS najdete v článku [Automatizace nasazení virtuálního počítače ve službě Amazon Web Services](automation-scenario-aws-deployment.md).

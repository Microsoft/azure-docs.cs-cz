---
title: Azure Stack úložiště pověření instančního objektu ve službě Key Vault | Dokumentace Microsoftu
description: Zjistěte, jak Key Vault ukládá přihlašovací údaje instančního objektu služby ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: sethm
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: d4bc560a1c607d4a12cfb3d08ffeacf9b5d2196a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244269"
---
# <a name="store-service-principal-credentials-in-key-vault"></a>Store pověření instančního objektu ve službě Key Vault

Vývoj aplikací v Azure stacku obvykle vyžaduje vytváření instančního objektu a pomocí těchto přihlašovacích údajů k ověření před nasazením. Však často uložené přihlašovací údaje pro instanční objekt se k jejich chybnému umístění. Tento článek popisuje, jak vytvořit instanční objekt a uložit pro pozdější načtení hodnoty ve službě Azure Key Vault.

Další informace o službě Key Vault najdete v tématu [v tomto článku](azure-stack-key-vault-intro.md).

## <a name="prerequisites"></a>Požadavky

- Předplatné nabídky, která zahrnuje službu Azure Key Vault.
- PowerShell je nakonfigurován pro použití se službou Azure Stack.

## <a name="key-vault-in-azure-stack"></a>Trezor klíčů ve službě Azure Stack

Key Vault ve službě Azure Stack pomáhá chránit kryptografické klíče a tajné kódy, které cloudové aplikace a služby používat. Pomocí služby Key Vault můžete šifrovat klíče a tajné kódy.

Chcete-li vytvořit trezor klíčů, postupujte takto:

1. Přihlaste se k portálu Azure Stack.

2. Z řídicího panelu, vyberte **+ vytvořit prostředek**, pak **zabezpečení + Identita**a pak vyberte **služby Key Vault.**

   ![Vytvoření trezoru klíčů](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. V **vytvořit trezor klíčů** podokno, přiřadit **název** pro svůj trezor. Trezor názvy můžou obsahovat jenom alfanumerické znaky a znak spojovníku (-). Nesmí začínat číslicí.

4. V seznamu dostupných předplatných si vyberte požadované předplatné.

5. Vyberte existující skupinu prostředků nebo vytvořte novou.

6. Vyberte cenovou úroveň.

7. Vyberte jednu z existujících zásad přístupu nebo vytvořte novou. Zásady přístupu vám umožní udělit oprávnění pro uživatele, aplikace nebo skupinu zabezpečení k provedení operací k tomuto trezoru.

8. Volitelně můžete zvolte zásadu přístupu advanced. pro povolení přístupu k funkcím.

9. Jakmile konfigurujete nastavení, vyberte **OK**a pak vyberte **vytvořit**. Spustí se nasazení služby key vault.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

1. Přihlaste se ke svému účtu Azure na webu Azure portal.

2. Vyberte **Azure Active Directory**, pak **registrace aplikací**, pak **přidat**.

3. Zadejte název a URL aplikace. Vyberte buď **webovou aplikaci nebo API** nebo **nativní** pro typ aplikace, kterou chcete vytvořit. Po nastavení hodnot, vyberte **vytvořit**.

4. Vyberte **služby Active Directory**, pak **registrace aplikací**a vyberte svou aplikaci.

5. Zkopírujte **ID aplikace** a uložte ho v kódu aplikace. Aplikace v ukázkových aplikací používají **ID klienta** k odkazování na **ID aplikace**.

6. Pokud chcete generovat ověřovací klíč, vyberte **Klíče**.

7. Zadejte popis pro klíč a hodnotu duration.

8. Vyberte **Uložit**.

9. Kopírovat **klíč** , která bude k dispozici po klepnutí na **Uložit**.

## <a name="store-the-service-principal-inside-key-vault"></a>Store instanční objekt služby ve službě Key Vault

1. Přihlaste se k portálu user portal pro Azure Stack, pak vyberte trezor klíčů, který jste vytvořili dříve a pak vyberte **tajný kód** dlaždici.

2. V **tajný kód** vyberte **vygenerovat/importovat**.

3. V **vytvoření tajného klíče** podokno, ze seznamu možností vyberte **ruční**. Pokud jste vytvořili službu objektu zabezpečení pomocí certifikátů, certifikáty vyberte z rozevíracího seznamu a pak nahrajte soubor.

4. Zadejte **ID aplikace** zkopírován z instančního objektu jako název pro váš klíč. Název klíče, který může obsahovat jenom alfanumerické znaky a znak spojovníku (-).

5. Vložte hodnotu váš klíč z objektu do služby **hodnotu** kartu.

6. Vyberte **instanční objekt služby** pro **typ obsahu**.

7. Nastavte **datum aktivace** a **datum vypršení platnosti** hodnoty klíče.

8. Vyberte **vytvořit** ke spuštění nasazení.

Po úspěšném vytvoření tajného klíče se uloží existuje informací o instančním objektu služby. Můžete zvolit kdykoli pod **tajných kódů**a zobrazit nebo upravit její vlastnosti. Oddíl properties obsahuje identifikátor tajného kódu, který je identifikátor URI (Uniform Resource), které externí aplikace použít pro přístup k tento tajný kód.

## <a name="next-steps"></a>Další postup

- [Použít instanční objekty](azure-stack-create-service-principals.md)
- [Správa služby Key Vault ve službě Azure Stack pomocí portálu](azure-stack-key-vault-manage-portal.md)  
- [Správa služby Key Vault ve službě Azure Stack pomocí prostředí PowerShell](azure-stack-key-vault-manage-powershell.md)
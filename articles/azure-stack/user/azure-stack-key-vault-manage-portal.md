---
title: Správa služby Key Vault ve službě Azure Stack pomocí portálu | Dokumentace Microsoftu
description: Další informace o správě služby Key Vault ve službě Azure Stack pomocí portálu
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/201
ms.author: sethm
ms.openlocfilehash: 5e0462c533be11241908cafc593c89588f61f349
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192190"
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Správa služby Key Vault ve službě Azure Stack pomocí portálu

Key Vault ve službě Azure Stack můžete spravovat pomocí portálu Azure Stack. Tento článek popisuje, jak vytvářet a spravovat služby key vault ve službě Azure Stack.

## <a name="prerequisites"></a>Požadavky

Můžete musí přihlásit k odběru nabídky, která zahrnuje službu Azure Key Vault.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

1. Přihlaste se k [portálu user portal](https://portal.local.azurestack.external).

2. Z řídicího panelu, vyberte **+ vytvořit prostředek**, pak **zabezpečení + Identita**, pak **služby Key Vault**.

    ![Obrazovky služby Key Vault](media/azure-stack-key-vault-manage-portal/image1.png)

3. V **vytvořit trezor klíčů** podokno, přiřadit **název** pro svůj trezor. Trezor názvy můžou obsahovat jenom alfanumerické znaky a znak spojovníku (-). Nesmí začínat číslicí.

4. Zvolte **předplatné** ze seznamu dostupných předplatných. V rozevíracím seznamu se zobrazí všechna předplatná, která nabízí služby Key Vault.

5. Vyberte existující **skupiny prostředků**, nebo vytvořte novou.

6. Vyberte **cenová úroveň**. Trezory klíčů v Azure Stack Development Kit (ASDK), podporu **standardní** pouze skladové položky.

7. Vyberte jednu z existující **zásady přístupu** nebo vytvořte novou. Zásady přístupu můžete udělit oprávnění pro uživatele, aplikace nebo skupinu zabezpečení k provedení operací k tomuto trezoru.

8. Volitelně vyberte možnost **zásad přístupu Advanced** umožňuje přístup k funkcím. Příklad: virtuální počítače (VM) pro nasazení Resource Manageru pro nasazení šablony a přístup k Azure Disk Encryption pro šifrování svazku.

9. Jakmile konfigurujete nastavení, vyberte **OK**a pak vyberte **vytvořit**. Spustí se nasazení služby key vault.

## <a name="manage-keys-and-secrets"></a>Správa klíčů a tajných kódů

Po vytvoření trezoru, použijte následující postup k vytváření a správě klíčů a tajných kódů v trezoru.

### <a name="create-a-key"></a>Vytvoření klíče

1. Přihlaste se k [portálu user portal](https://portal.local.azurestack.external).

2. Z řídicího panelu, vyberte **všechny prostředky**, vyberte trezor klíčů, který jste vytvořili dříve a pak vyberte **klíče** dlaždici.

3. V **klíče** vyberte **přidat**.

4. V **vytvořte klíč** podokno, ze seznamu **možnosti**, zvolte metodu, kterou chcete použít k vytvoření klíče. Je možné **generovat** nový klíč, **nahrát** z existujícího klíče, nebo použijte **obnovit zálohování** vyberte zálohování klíče.

5. Zadejte **název** pro váš klíč. Název klíče, který může obsahovat jenom alfanumerické znaky a znak spojovníku (-).

6. Volitelně můžete nakonfigurovat **nastavit datum aktivace** a **nastavit datum vypršení platnosti** hodnoty klíče.

7. Vyberte **vytvořit** ke spuštění nasazení.

Po úspěšném vytvoření klíče můžete vybrat za **klíče** a zobrazit nebo upravit její vlastnosti. Obsahuje oddíl properties **identifikátor klíče**, což je identifikátor URI (Uniform Resource), které externí aplikace použít pro přístup k tohoto klíče. K omezení týkající se tohoto klíče, nakonfigurujte nastavení v části **povolené operace**.

![Identifikátor URI klíče](media/azure-stack-key-vault-manage-portal/image4.png)

### <a name="create-a-secret"></a>Vytvoření tajného klíče

1. Přihlaste se k [portálu user portal](https://portal.local.azurestack.external).

2. Z řídicího panelu, vyberte **všechny prostředky**, vyberte trezor klíčů, který jste vytvořili dříve a pak vyberte **tajných kódů** dlaždici.

3. V části **tajných kódů**vyberte **přidat**.

4. V části **vytvoření tajného klíče**, ze seznamu **možnosti nahrání**, zvolte možnost, se kterým se má vytvořit tajný kód. Můžete vytvořit tajný kód **ručně** Pokud zadáte hodnotu pro tajný klíč nebo nahrávání **certifikát** z místního počítače.

5. Zadejte **název** pro tajný kód. Název tajného kódu může obsahovat jenom alfanumerické znaky a znak spojovníku (-).

6. Volitelně můžete určit **typ obsahu**a nakonfigurujte hodnoty pro **nastavit datum aktivace** a **nastavit datum vypršení platnosti** pro tajný kód.

7. Vyberte **vytvořit** ke spuštění nasazení.

Po úspěšném vytvoření tajného klíče můžete vybrat za **tajných kódů** a zobrazit nebo upravit její vlastnosti. **Identifikátor tajného kódu** je identifikátor URI, který externí aplikace můžete použít pro přístup k tento tajný kód.

![Identifikátor URI tajného klíče](media/azure-stack-key-vault-manage-portal/image5.png)

## <a name="next-steps"></a>Další postup

* [Nasazení virtuálního počítače při získání hesel uložených v Key Vault](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Nasazení virtuálního počítače pomocí certifikátu uloženého ve službě Key Vault](azure-stack-key-vault-push-secret-into-vm.md)

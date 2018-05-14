---
title: Spravovat pomocí portálu v zásobníku Azure Key Vault | Microsoft Docs
description: Zjistěte, jak spravovat Key Vault v zásobníku Azure pomocí portálu
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: mabrigg
ms.openlocfilehash: e387e365b2de3412c23c995939f75400a03eeb80
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Spravovat Key Vault v zásobníku Azure pomocí portálu

V zásobníku Azure Key Vault můžete spravovat pomocí portálu Azure zásobníku. Tento článek vám pomůže začít pracovat k vytváření a správě trezoru klíčů v zásobníku Azure.

## <a name="prerequisites"></a>Požadavky

Musí se přihlásíte k nabídku, která zahrnuje službu Azure Key Vault.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

1. Přihlaste se k [portálu user portal](https://portal.local.azurestack.external).

2. Na řídicím panelu, vyberte **nový** > **zabezpečení a identita** > **Key Vault**.

    ![Obrazovka Key Vault](media/azure-stack-kv-manage-portal/image1.png)

3. V **vytvořit Key Vault** podokně přiřadit **název** pro svůj trezor. Názvy úložiště může obsahovat pouze alfanumerické znaky a speciální znak pomlčka (-). Nesmí začínat číslicí.

4. Vyberte **předplatné** ze seznamu dostupných předplatných. V rozevíracím seznamu se zobrazí všechny odběry, které nabízejí služby Key Vault.

5. Vyberte existující **skupiny prostředků** nebo vytvořte novou.

6. Vyberte **cenová úroveň**.
    >[!NOTE]
    > Klíč trezorů v podpoře Azure zásobníku Development Kit **standardní** jenom jednotky SKU.

7. Vyberte jednu z existujících **zásady přístupu** nebo vytvořte novou. Zásady přístupu umožňuje udělit oprávnění pro uživatele, aplikaci nebo skupinu zabezpečení, k provádění operací s Tento trezor.

8. Volitelně můžete zvolit **zásady rozšířené přístupu** pro povolení přístupu k funkcím. Příklad: virtuální počítače (VM) pro nasazení, Resource Manager pro nasazení šablony a přístup k Azure Disk Encryption pro šifrování svazku.

9. Po dokončení konfigurace nastavení, vyberte **OK**a potom vyberte **vytvořit**. Tím se spustí nasazení trezoru klíčů.

## <a name="manage-keys-and-secrets"></a>Správa klíčů a tajných klíčů

Po vytvoření trezoru, použijte následující postup vytvoření a Správa klíčů a tajných klíčů v trezoru.

### <a name="create-a-key"></a>Vytvoření klíče

1. Přihlaste se k [portálu user portal](https://portal.local.azurestack.external).

2. Na řídicím panelu, vyberte **všechny prostředky**, vyberte trezor klíčů, který jste předtím vytvořili a pak vyberte **klíče** dlaždici.

3. V **klíče** podokně, vyberte **přidat**.

4. V **vytvořte klíč** podokně ze seznamu **možnosti**, zvolte metodu, kterou chcete použít k vytvoření klíče. Můžete **generování** nový klíč, **nahrát** existující klíče, nebo použijte **obnovit zálohování** vyberte zálohu klíče.

5. Zadejte **název** pro váš klíč. Název klíče může obsahovat pouze alfanumerické znaky a speciální znak pomlčka (-).

6. Volitelně můžete nakonfigurovat **nastavit datum aktivace** a **nastavit datum vypršení platnosti** hodnoty klíče.

7. Vyberte **vytvořit** ke spuštění nasazení.

Po úspěšném vytvoření klíče, můžete ji v části vybrat **klíče** a zobrazit nebo upravit jeho vlastnosti. V části vlastnosti obsahuje **identifikátor klíče**, což je identifikátor URI (Uniform Resource), externí aplikace použít pro přístup k tento klíč. Chcete-li omezit operace pro tento klíč, nakonfigurujte nastavení v části **povolené operace**.

![Identifikátor URI klíče](media/azure-stack-kv-manage-portal/image4.png)

### <a name="create-a-secret"></a>Vytvoření tajného klíče

1. Přihlaste se k [portálu user portal](https://portal.local.azurestack.external).
2. Na řídicím panelu, vyberte **všechny prostředky**, vyberte trezor klíčů, který jste předtím vytvořili a pak vyberte **tajné klíče** dlaždici.

3. V části **tajné klíče**, vyberte **přidat**.

4. V části **vytvoření tajného klíče**, ze seznamu **možnosti odesílání**, zvolte možnost, podle kterého chcete vytvořit tajný klíč. Můžete vytvořit tajný klíč **ručně** Pokud zadáte hodnotu pro tajný klíč nebo odeslání **certifikát** z místního počítače.

5. Zadejte **název** pro tajný klíč. Tajný název může obsahovat pouze alfanumerické znaky a speciální znak pomlčka (-).

6. Volitelně můžete určit **typ obsahu**a konfigurovat hodnoty pro **nastavit datum aktivace** a **nastavit datum vypršení platnosti** pro tajný klíč.

7. Vyberte **vytvořit** ke spuštění nasazení.

Po úspěšném vytvoření tajný klíč, můžete ji v části vybrat **tajné klíče** a zobrazit nebo upravit jeho vlastnosti. **Tajný klíč identifikátor** je identifikátor URI, který externí aplikace můžete použít pro přístup tento tajný klíč.

![Identifikátor URI tajného klíče](media/azure-stack-kv-manage-portal/image5.png)

## <a name="next-steps"></a>Další postup

* [Nasazení virtuálního počítače s načtením heslo uložené v Key Vault](azure-stack-kv-deploy-vm-with-secret.md)
* [Nasadit virtuální počítač s certifikát uložený v Key Vault](azure-stack-kv-push-secret-into-vm.md)

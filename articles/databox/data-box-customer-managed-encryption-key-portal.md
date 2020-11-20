---
title: Použití Azure Portal ke správě klíčů spravovaných zákazníkem pro Azure Data Box
description: Naučte se používat Azure Portal k vytváření a správě klíčů spravovaných zákazníkem pomocí Azure Key Vault pro Azure Data Box. Klíče spravované zákazníkem vám umožní vytvořit, otočit, zakázat a odvolat řízení přístupu.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: cd9f4ad6b6831b2b15c09b37edc569b3f2d247f7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958006"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Použití klíčů spravovaných zákazníkem v Azure Key Vault pro Azure Data Box

Azure Data Box chrání klíč pro odemčení zařízení (označovaný také jako heslo zařízení), který se používá k uzamčení zařízení prostřednictvím šifrovacího klíče. Ve výchozím nastavení je tento šifrovací klíč spravovaným klíčem společnosti Microsoft. Pro další kontrolu můžete použít klíč spravovaný zákazníkem.

Použití klíče spravovaného zákazníkem nemá vliv na to, jak se data v zařízení šifrují. Ovlivňuje pouze to, jak je klíč odemčení zařízení zašifrovaný.

Chcete-li zachovat tuto úroveň řízení v rámci procesu objednávky, použijte při vytváření objednávky klíč spravovaný zákazníkem. Další informace najdete v tématu [kurz: Azure Data box Order](data-box-deploy-ordered.md).

V tomto článku se dozvíte, jak povolit klíč spravovaný zákazníkem pro stávající pořadí Data Box v [Azure Portal](https://portal.azure.com/). Zjistíte, jak změnit Trezor klíčů, klíč, verzi nebo identitu pro aktuální klíč spravovaný zákazníkem nebo přejít zpět na použití spravovaného klíče společnosti Microsoft.

Tento článek se týká Azure Data Box a Azure Data Box Heavych zařízení.

## <a name="requirements"></a>Požadavky

Klíč spravovaný zákazníkem pro objednávku Data Box musí splňovat následující požadavky:

- Klíč musí být vytvořený a uložený v Azure Key Vault, který má **obnovitelné odstranění** a povolený **mazání** . Další informace najdete v článku [Co je Azure Key Vault](../key-vault/general/overview.md). Při vytváření nebo aktualizaci vaší objednávky můžete vytvořit Trezor klíčů a klíč.

- Klíč musí být klíčem RSA o velikosti 2048 nebo větší.

## <a name="enable-key"></a>Povolit klíč

Pokud chcete povolit klíč spravovaný zákazníkem pro existující pořadí Data Box v Azure Portal, postupujte takto:

1. Přejdete na obrazovku **Přehled** pro vaši data Boxou objednávku.

    ![Přehled obrazovky Data Boxho pořadí – 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Přejít na **nastavení > šifrování** a vyberte **klíč spravovaný zákazníkem**. Pak vyberte **Vybrat klíč a trezor klíčů**.

    ![Vybrat možnost šifrování klíče spravovaného zákazníkem](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   Na obrazovce **Vybrat klíč z Azure Key Vault** se vaše předplatné automaticky vyplní.

 3. V případě **trezoru klíčů** můžete v rozevíracím seznamu vybrat existující Trezor klíčů nebo vybrat **vytvořit nové** a vytvořit nový trezor klíčů.

     ![Možnosti trezoru klíčů při výběru klíče spravovaného zákazníkem](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     Pokud chcete vytvořit nový trezor klíčů, zadejte předplatné, skupinu prostředků, název trezoru klíčů a další informace na obrazovce **Vytvoření nové trezoru klíčů** . V **možnostech obnovení** zajistěte, aby byla povolená ochrana proti **odstranění** a **vyprázdnění** . Pak vyberte **zkontrolovat + vytvořit**.

      ![Kontrola a vytváření Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      Zkontrolujte informace o vašem trezoru klíčů a vyberte **vytvořit**. Počkejte pár minut, než se vytváření trezoru klíčů dokončí.

       ![Vytvoření Azure Key Vault s nastavením](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. Na obrazovce **Vybrat klíč z Azure Key Vault** můžete vybrat existující klíč z trezoru klíčů nebo vytvořit nový.

    ![Vyberte klíč z Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   Pokud chcete vytvořit nový klíč, vyberte **vytvořit novou**. Je nutné použít klíč RSA. Velikost může být 2048 nebo vyšší.

    ![Vytvořit nový klíč v Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    Zadejte název nového klíče, Přijměte ostatní výchozí hodnoty a vyberte **vytvořit**. Budete upozorněni, že se ve vašem trezoru klíčů vytvořil klíč.

    ![Nový klíč s názvem](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. V případě **verze** můžete z rozevíracího seznamu vybrat existující verzi klíče.

    ![Vybrat verzi pro nový klíč](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    Pokud chcete vygenerovat novou verzi klíče, vyberte **vytvořit novou**.

    ![Otevření dialogového okna pro vytvoření nové verze klíče](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    Zvolte nastavení pro novou verzi klíče a vyberte **vytvořit**.

    ![Vytvořit novou verzi klíče](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. Když vyberete Trezor klíčů, klíč a verzi klíče, zvolte **Vybrat**.

    ![Klíč v Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    Nastavení **typ šifrování** zobrazuje Trezor klíčů a klíč, který jste zvolili.

    ![Klíč a trezor klíčů pro klíč spravovaný zákazníkem](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. Vyberte typ identity, který se použije ke správě klíče spravovaného zákazníkem pro tento prostředek. Můžete použít identitu **přiřazenou systémem** , která byla vygenerována během vytváření objednávky nebo zvolit identitu přiřazenou uživatelem.

    Identita přiřazená uživatelem je nezávislý prostředek, který můžete použít ke správě přístupu k prostředkům. Další informace najdete v tématu [spravované typy identit](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Vyberte typ identity.](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    Chcete-li přiřadit identitu uživatele, vyberte možnost **přiřazený uživatel**. Pak vyberte **Vybrat identitu uživatele** a vyberte spravovanou identitu, kterou chcete použít.

    ![Vyberte identitu, kterou chcete použít.](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    Tady nemůžete vytvořit novou identitu uživatele. Pokud chcete zjistit, jak ho vytvořit, přečtěte si téma [Vytvoření, výpis, odstranění nebo přiřazení role k spravované identitě přiřazené uživatelem pomocí Azure Portal](/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal).

    Vybraná identita uživatele se zobrazí v nastavení **typu šifrování** .

    ![Vybraná identita uživatele zobrazená v nastavení typ šifrování](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. Výběrem možnosti **Uložit** uložte aktualizované nastavení **typu šifrování** .

     ![Uložení klíče spravovaného zákazníkem](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    Adresa URL klíče se zobrazí v části **typ šifrování**.

    ![Adresa URL klíče spravovaného zákazníkem](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>Změnit klíč

Pokud chcete změnit Trezor klíčů, klíč nebo verzi klíče pro klíč spravovaný zákazníkem, který aktuálně používáte, postupujte takto:

1. Na obrazovce **Přehled** pro pořadí data box přejděte na **Nastavení**  >  **šifrování** a klikněte na **změnit klíč**.

    ![Přehledová obrazovka Data Boxho pořadí s klíčem spravovaným zákazníkem-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. Zvolte **vybrat jiný Trezor klíčů a klíč**.

    ![Obrazovka s přehledem Data Boxho pořadí, vyberte jiný klíč a možnost trezoru klíčů.](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. Obrazovka **Vybrat klíč z trezoru klíčů** zobrazuje předplatné, ale ne Trezor klíčů, klíč nebo verzi klíče. Můžete provést následující změny:

   - Vyberte jiný klíč ze stejného trezoru klíčů. Před výběrem klíče a verze bude nutné vybrat Trezor klíčů.

   - Vyberte jiný Trezor klíčů a přiřaďte nový klíč.

   - Změní verzi aktuálního klíče.
   
    Až změny dokončíte, zvolte **Vybrat**.

    ![Výběr možnosti šifrování – 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. Vyberte **Uložit**.

    ![Uložit aktualizované nastavení šifrování-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>Změnit identitu

Chcete-li změnit identitu používanou ke správě přístupu k hlavnímu klíči spravovanému zákazníkem v tomto pořadí, postupujte podle následujících kroků:

1. Na obrazovce **Přehled** dokončeného data Boxho pořadí, přejít na **Nastavení**  >  **šifrování**.

2. Proveďte jednu z následujících změn:

     - Pokud chcete přejít na jinou identitu uživatele, klikněte na **vybrat jinou identitu uživatele**. Pak vyberte jinou identitu na panelu na pravé straně obrazovky a zvolte **Vybrat**.

       ![Možnost změny uživatelsky přiřazené identity pro klíč spravovaný zákazníkem](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - Pokud chcete přepnout na identitu přiřazenou systémem, která se vygenerovala během vytváření objednávky, vyberte **systém přiřazený** pomocí **Vybrat typ identity**.

     ![Možnost přechodu na systém přiřazený ke klíči spravovanému zákazníkem](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. Vyberte **Uložit**.

    ![Uložit aktualizované nastavení šifrování – 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>Použít spravovaný klíč Microsoft

Pokud chcete změnit použití klíče spravovaného zákazníkem na spravovaný klíč společnosti Microsoft pro vaši objednávku, postupujte takto:

1. Na obrazovce **Přehled** dokončeného data Boxho pořadí, přejít na **Nastavení**  >  **šifrování**.

2. Vyberte **typ**, vyberte možnost **spravovaný klíč společnosti Microsoft**.

    ![Přehled obrazovky Data Boxho řádu – 5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. Vyberte **Uložit**.

    ![Uložení aktualizovaných nastavení šifrování pro spravovaný klíč společnosti Microsoft](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>Řešení chyb

Pokud obdržíte nějaké chyby týkající se vašeho klíče spravovaného zákazníkem, použijte následující tabulku k řešení potíží.

| Kód chyby| Podrobnosti o chybě| Obnovitelné?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Nepovedlo se načíst klíč, protože je zakázaný spravovaný klíč zákazníka.| Ano, povolením verze klíče.|
| SsemUserErrorEncryptionKeyExpired| Nepovedlo se načíst klíč, protože vypršela platnost klíče spravovaného zákazníkem.| Ano, povolením verze klíče.|
| SsemUserErrorKeyDetailsNotFound| Klíč nešlo načíst, protože se nepovedlo najít spravovaný klíč zákazníka.| Pokud jste odstranili Trezor klíčů, nemůžete obnovit klíč spravovaný zákazníkem.  Pokud jste tento trezor klíčů migrovali do jiného tenanta, přečtěte si téma [Změna ID tenanta trezoru klíčů po přesunu předplatného](../key-vault/general/move-subscription.md). Pokud jste odstranili Trezor klíčů:<ol><li>Ano, pokud je v době trvání ochrany vyprázdnění, postupujte podle kroků v části [obnovení trezoru klíčů](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Ne, pokud je mimo dobu trvání ochrany vyprázdnění.</li></ol><br>V opačném případě, pokud Trezor klíčů prošl při migraci tenanta, můžete ho obnovit pomocí jednoho z následujících kroků: <ol><li>Obnovte Trezor klíčů zpátky na starého tenanta.</li><li>Nastavte `Identity = None` a pak nastavte hodnotu zpět na `Identity = SystemAssigned` . Tím se po vytvoření nové identity odstraní a znovu vytvoří identita. Povolte `Get` , `Wrap` a `Unwrap` oprávnění k nové identitě v zásadách přístupu trezoru klíčů.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | Byl použit klíč spravovaný zákazníkem, ale přístup k klíči nebyl udělen nebo byl odvolán nebo nebylo možné získat přístup k trezoru klíčů, protože je povolená brána firewall. | Přidejte identitu vybranou k vašemu trezoru klíčů, aby bylo možné povolit přístup ke spravovanému klíči zákazníka. Pokud má Trezor klíčů zapnutou bránu firewall, přepněte na identitu přiřazenou systémem a pak přidejte spravovaný klíč zákazníka. Další informace najdete v tématu Jak [klíč povolit](#enable-key). |
| SsemUserErrorKeyVaultDetailsNotFound| Nepovedlo se načíst klíč, protože se nepovedlo najít přidružený Trezor klíčů pro spravovaný klíč zákazníka. | Pokud jste odstranili Trezor klíčů, nemůžete obnovit klíč spravovaný zákazníkem.  Pokud jste tento trezor klíčů migrovali do jiného tenanta, přečtěte si téma [Změna ID tenanta trezoru klíčů po přesunu předplatného](../key-vault/general/move-subscription.md). Pokud jste odstranili Trezor klíčů:<ol><li>Ano, pokud je v době trvání ochrany vyprázdnění, postupujte podle kroků v části [obnovení trezoru klíčů](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Ne, pokud je mimo dobu trvání ochrany vyprázdnění.</li></ol><br>V opačném případě, pokud Trezor klíčů prošl při migraci tenanta, můžete ho obnovit pomocí jednoho z následujících kroků: <ol><li>Obnovte Trezor klíčů zpátky na starého tenanta.</li><li>Nastavte `Identity = None` a pak nastavte hodnotu zpět na `Identity = SystemAssigned` . Tím se po vytvoření nové identity odstraní a znovu vytvoří identita. Povolte `Get` , `Wrap` a `Unwrap` oprávnění k nové identitě v zásadách přístupu trezoru klíčů.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Klíč nešlo načíst, protože se nepovedlo najít spravovaný klíč zákazníka.| Ano, zjistit, zda: <ol><li>Trezor klíčů má stále instalační soubor MSI v zásadách přístupu.</li><li>Identita je typu přiřazeno systémem.</li><li>Povolte oprávnění získat, zabalit a zrušit zalomení identity v zásadách přístupu trezoru klíčů.</li></ol>|
| SsemUserErrorUserAssignedLimitReached | Přidání nové identity přiřazené uživatelem se nepovedlo, protože jste dosáhli limitu celkového počtu identit přiřazených uživateli, které se dají přidat. | Zkuste prosím operaci zopakovat s menším počtem uživatelských identit nebo před opakováním pokusu z prostředku odebrat některé identity přiřazené uživateli. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Operace přístupu ke spravované identitě se nezdařila. <br> Poznámka: Jedná se o situaci, kdy se předplatné přesune do jiného tenanta. Zákazník musí ručně přesunout identitu do nového tenanta. Další podrobnosti najdete v PFA mailu. | Přesuňte prosím identitu vybranou na nového tenanta, pod nímž je předplatné k dispozici. Další informace najdete v tématu Jak [klíč povolit](#enable-key). |
| SsemUserErrorKekUserIdentityNotFound | Použil se spravovaný klíč zákazníka, ale identita přiřazená uživateli, která má přístup ke klíči, se ve službě Active Directory nenašla. <br> Poznámka: platí to pro případ, že se z Azure odstraní identita uživatele.| Zkuste prosím do trezoru klíčů přidat jinou identitu přiřazenou uživatelem a povolit přístup ke spravovanému klíči zákazníka. Další informace najdete v tématu Jak [klíč povolit](#enable-key). |
| SsemUserErrorUserAssignedIdentityAbsent | Klíč nešlo načíst, protože se nepovedlo najít spravovaný klíč zákazníka. | Nelze získat přístup ke spravovanému klíči zákazníka. Odstraní se buď identita přiřazená uživatelem (UAI) přidružená k tomuto klíči, nebo se změní typ UAI. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Operace přístupu ke spravované identitě se nezdařila. <br> Poznámka: Jedná se o situaci, kdy se předplatné přesune do jiného tenanta. Zákazník musí ručně přesunout identitu do nového tenanta. Další podrobnosti najdete v PFA mailu. | Zkuste prosím do trezoru klíčů přidat jinou identitu přiřazenou uživatelem a povolit přístup ke spravovanému klíči zákazníka. Další informace najdete v tématu Jak [klíč povolit](#enable-key).|
| SsemUserErrorKeyVaultBadRequestException | Byl použit klíč spravovaný zákazníkem, ale přístup k klíči nebyl udělen nebo byl odvolán nebo nebylo možné získat přístup k trezoru klíčů, protože je povolená brána firewall. | Přidejte identitu vybranou k vašemu trezoru klíčů, aby bylo možné povolit přístup ke spravovanému klíči zákazníka. Pokud má Trezor klíčů zapnutou bránu firewall, přepněte na identitu přiřazenou systémem a pak přidejte spravovaný klíč zákazníka. Další informace najdete v tématu Jak [klíč povolit](#enable-key). |
| Obecná chyba  | Nepovedlo se načíst klíč.| Toto je obecná chyba. Chcete-li vyřešit chybu a určit další kroky, obraťte se na podpora Microsoftu.|

## <a name="next-steps"></a>Další kroky

- [Co je Azure Key Vault?](../key-vault/general/overview.md)
- [Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webu Azure Portal](../key-vault/secrets/quick-create-portal.md)

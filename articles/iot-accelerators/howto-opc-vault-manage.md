---
title: Jak spravovat službu OPC trezor certifikátů – Azure | Microsoft Docs
description: Správa certifikátů kořenové certifikační autority OPC trezoru a uživatelských oprávnění
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 4420e5b0d895f8ea30dbd39fc50dd7480d57d086
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69995994"
---
# <a name="how-to-manage-the-opc-vault-certificate-service"></a>Jak spravovat službu OPC trezor certifikátů

Tento článek popisuje úlohy správy pro službu správy certifikátů trezoru OPC v Azure, obnovení certifikátů certifikační autority vystavitele, obnovení seznamu odvolaných certifikátů (CRL) a postup udělení a odvolání přístupu uživatele.

## <a name="create-or-renew-the-root-ca-certificate"></a>Vytvořit nebo obnovit certifikát kořenové certifikační autority

Vytvoření kořenového certifikátu certifikační autority je povinný krok po nasazení. Bez platného certifikátu certifikační autority vystavitele není možné podepisovat a vystavovat žádné certifikáty aplikací.<br>Informace o [životnosti certifikátů](howto-opc-vault-secure-ca.md#certificates) pro správu certifikátů s rozumnými a bezpečnými životnostmi najdete v části. Certifikát certifikační autority vystavitele by měl být obnoven po polovině své životnosti, ale ne později než konfigurovaná doba platnosti nově podepsaného certifikátu aplikace by překročila dobu života certifikátu vystavitele.<br>
> [!IMPORTANT]
> K vytvoření nebo obnovení certifikátu certifikační autority vystavitele je nutná role správce.

1. Otevřete službu Certificate Service na `https://myResourceGroup-app.azurewebsites.net` adrese a přihlaste se.
2. Přejděte na `Certificate Groups` stránku.
3. Je uvedena jedna `Default` skupina certifikátů. Klikněte na `Edit`.
4. V `Edit Certificate Group Details` nástroji můžete upravit název subjektu a dobu života vaší certifikační autority a certifikátů aplikací.<br>Předmět a životnost by se měly nastavit jenom jednou, než se vystaví první certifikát certifikační autority. Změny doby života během operací můžou vést k nekonzistentním životnosti vydaných certifikátů a seznamů CRL.
5. Zadejte platný předmět, například `CN=My CA Root, O=MyCompany, OU=MyDepartment`.<br>
   > [!IMPORTANT]
   > Změna předmětu vyžaduje, aby se obnovil certifikát vystavitele, jinak se službě nepodaří podepsat certifikáty aplikací. Předmět konfigurace je správnosti zkontrolován podle předmětu aktivního certifikátu vystavitele. Pokud se subjekty neshodují, podepsání certifikátu je odmítnuto.
6. Klikněte na tlačítko `Save` .
7. Pokud v tomto okamžiku dojde k chybě "zakázáno", přihlašovací údaje uživatele nemají oprávnění správce pro úpravu nebo vytvoření nového kořenového certifikátu. Uživatel, který službu nasadil, má ve výchozím nastavení role správce a podepisování pomocí služby, ostatní uživatelé musí být v rámci registrace aplikace AzureAD přidáni do rolí ' schvalovatel ', ' Writer ' nebo ' Administrator '.
8. Klikněte na tlačítko `Details` . Měl `View Certificate Group Details` by se zobrazit aktualizované informace.
9. Kliknutím na `Renew CA Certificate` tlačítko vydáte první certifikát certifikační autority vystavitele nebo obnovíte certifikát vystavitele. Pokračujte `Ok` stisknutím klávesy.
10. Po několika sekundách `Certificate Details` se zobrazí. Stisknutím `Issuer` nebo`Crl` Stáhněte nejnovější certifikát certifikační autority a seznam CRL pro distribuci do vašich aplikací OPC UA.
11. Služba OPC UA Certificate Management je teď připravená k vydávání certifikátů pro aplikace OPC UA.

## <a name="renew-the-crl"></a>Obnovit seznam CRL

Obnovení seznamu odvolaných certifikátů (CRL) je aktualizace, která by se měla distribuovat do aplikací v pravidelných intervalech. Zařízení OPC UA, která podporují rozšíření x509 distribučního bodu seznamu CRL, můžou přímo aktualizovat seznam odvolaných certifikátů z koncového bodu mikroslužeb. Jiná zařízení OPC UA můžou vyžadovat ruční aktualizace nebo v nejlepším případě je můžete aktualizovat pomocí rozšíření nabízených oznámení GDS serveru (*) a aktualizovat seznamy důvěryhodných certifikátů pomocí certifikátů a seznamů CRL.

V následujícím pracovním postupu jsou všechny žádosti o certifikát v odstraněných stavech odvolány v seznamech CRL, které odpovídají certifikátu certifikační autority vystavitele, pro které byly vystaveny. Číslo verze seznamu odvolaných certifikátů se zvyšuje o 1. <br>
> [!NOTE]
> Všechny vydané seznamy odvolaných certifikátů jsou platné až do vypršení platnosti certifikátu certifikační autority vystavitele, protože specifikace OPC UA nevyžaduje pro seznam CRL povinný a deterministický distribuční model.

> [!IMPORTANT]
> K obnovení seznamu CRL vystavitele je nutná role správce.

1. Otevřete službu Certificate Service na `https://myResourceGroup.azurewebsites.net` adrese a přihlaste se.
2. Přejděte na `Certificate Groups` stránku.
3. Klikněte na tlačítko `Details` . Měl `View Certificate Group Details` by se zobrazit aktuální certifikát a informace o seznamu CRL.
4. Klikněte na tlačítko a vydejte aktualizovaný seznam CRL pro všechny aktivní certifikáty vystavitelů v úložišti trezoru OPC. `Update CRL Revocation List(CRL)`
5. Po několika sekundách `Certificate Details` se zobrazí. Stisknutím `Issuer` nebo`Crl` Stáhněte nejnovější certifikát certifikační autority a seznam CRL pro distribuci do vašich aplikací OPC UA.

## <a name="manage-user-roles"></a>Správa rolí uživatelů

Role uživatelů pro mikroslužby trezoru OPC se spravují v Azure Active Directory podnikových aplikacích.

Podrobný popis definic rolí najdete v části [role](howto-opc-vault-secure-ca.md#roles) .

Ve výchozím nastavení se ověřený uživatel v tenantovi může ke službě přihlásit jako čtenář. Role s vyššími oprávněními vyžadují ruční správu v Azure Portal nebo pomocí PowerShellu.

### <a name="add-user"></a>Přidat uživatele

1. Otevřete Azure Portal v `portal.azure.com`.
2. Přejděte na `Azure Active Directory`. / `Enterprise applications`
3. Ve výchozím nastavení `resourceGroupName-service`vyberte registraci mikroslužby trezoru OPC.
4. Přejděte na adresu `Users and Groups`.
5. Klikněte na `Add User`.
6. Vyberte nebo Pozvěte uživatele k přiřazení ke konkrétní roli.
7. Vyberte roli pro uživatele.
8. `Assign` Stiskněte tlačítko.
9. Pro uživatele v `Administrator` roli `Approver` nebo role pokračujte v přidávání zásad Azure Key Vault přístupu.

### <a name="remove-user"></a>Odebrat uživatele

1. Otevřete Azure Portal v `portal.azure.com`.
2. Přejděte na `Azure Active Directory`. / `Enterprise applications`
3. Ve výchozím nastavení `resourceGroupName-service`vyberte registraci mikroslužby trezoru OPC.
4. Přejděte na adresu `Users and Groups`.
5. Vyberte uživatele s rolí, kterou chcete odebrat.
6. `Remove` Stiskněte tlačítko.
7. Odeberte taky odebrané správce a schvalovatele ze zásad Azure Key Vault.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Přidat zásady přístupu uživatele do Azure Key Vault

Pro schvalovatele a **správce**jsou vyžadovány další zásady přístupu.

Ve výchozím nastavení má identita služby pouze omezená oprávnění pro přístup k Key Vault, aby se předešlo operacím se zvýšenými oprávněními nebo změnám bez zosobnění uživatele. Základní oprávnění služby jsou `Get` a `List` pro tajné klíče a certifikáty. V případě tajných klíčů existuje jenom jedna výjimka, služba může `Delete` privátní klíč z úložiště tajného kódu po přijetí uživatelem přijmout. Všechny ostatní operace vyžadují oprávnění zosobněná uživatelem.<br>

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Pro **roli schvalovatele** musí být do Key Vault přidána následující oprávnění:

1. Otevřete Azure Portal v `portal.azure.com`.
2. Přejděte do vašeho trezoru `resourceGroupName`OPC použitého během nasazování.
3. Přejděte na Key Vault `resourceGroupName-xxxxx`.
4. Přejděte na `Access Policies`.
5. Klikněte na `Add new`.
6. Přeskočit šablonu, neexistuje žádná šablona, která by odpovídala požadavkům.
7. Klikněte na `Select Principal` a vyberte uživatele, který se má přidat, nebo pozvání nového uživatele k tenantovi.
8. Ověřte `Key permissions` :`Get` a`Sign`nejdůležitější. `List`
9. Ověřte `Secret permissions`: `Get`, `List`, a.`Set` `Delete`
10. Kontrolovat `Certificate permissions`: `Get`a .`List`
11. Klikněte na `Ok` (Další).
12. `Save`provedeny.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Pro **roli správce** musí být do Key Vault přidána následující oprávnění:

1. Otevřete Azure Portal v `portal.azure.com`.
2. Přejděte do vašeho trezoru `resourceGroupName`OPC použitého během nasazování.
3. Přejděte na Key Vault `resourceGroupName-xxxxx`.
4. Přejděte na `Access Policies`.
5. Klikněte na `Add new`.
6. Přeskočit šablonu, neexistuje žádná šablona, která by odpovídala požadavkům.
7. Klikněte na `Select Principal` a vyberte uživatele, který se má přidat, nebo pozvání nového uživatele k tenantovi.
8. Ověřte `Key permissions` :`Get` a`Sign`nejdůležitější. `List`
9. Ověřte `Secret permissions`: `Get`, `List`, a.`Set` `Delete`
10. Kontrolovat `Certificate permissions`: `Get`, `List`, `Update`a. `Create` `Import`
11. Klikněte na `Ok` (Další).
12. `Save`provedeny.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Odebrat zásady přístupu uživatele z Azure Key Vault

1. Otevřete Azure Portal v `portal.azure.com`.
2. Přejděte do vašeho trezoru `resourceGroupName`OPC použitého během nasazování.
3. Přejděte na Key Vault `resourceGroupName-xxxxx`.
4. Přejděte na `Access Policies`.
5. Vyhledejte uživatele, který chcete odebrat, a `... / Delete` klikněte na tlačítko pro odstranění přístupu uživatele.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak spravovat certifikáty a uživatele trezoru OPC, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Zabezpečená komunikace zařízení OPC](howto-opc-vault-secure.md)

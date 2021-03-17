---
title: 'Azure AD Connect: povolení zpětného zápisu zařízení | Microsoft Docs'
description: Tento dokument popisuje, jak povolit zpětný zápis zařízení pomocí Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d526394ac89e2d29b2002004736e8480bb15b954
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973418"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: povolení zpětného zápisu zařízení
> [!NOTE]
> Pro zpětný zápis zařízení se vyžaduje předplatné Azure AD Premium.
> 
> 

Následující dokumentace poskytuje informace o tom, jak povolit funkci zpětného zápisu zařízení v Azure AD Connect. Zpětný zápis zařízení se používá v následujících scénářích:

* Povolit [Windows Hello pro firmy pomocí nasazení hybridních důvěryhodných certifikátů](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration)
* Povolte podmíněný přístup na základě zařízení do služby AD FS (2012 R2 nebo vyšší) chráněných aplikací (vztahy důvěryhodnosti předávající strany).

To poskytuje další zabezpečení a jistotu, že přístup k aplikacím je udělen pouze důvěryhodným zařízením. Další informace o podmíněném přístupu najdete v tématech [Správa rizik pomocí podmíněného přístupu](../conditional-access/overview.md) a [nastavení místního podmíněného přístupu pomocí Azure Active Directory Device Registration](../devices/overview.md).

> [!IMPORTANT]
> <li>Zařízení se musí nacházet ve stejné doménové struktuře jako uživatelé. Vzhledem k tomu, že zařízení musí být zapsána zpět do jedné doménové struktury, tato funkce v současné době nepodporuje nasazení s více doménovými strukturami uživatelů.</li>
> <li>Do místní doménové struktury služby Active Directory se dá přidat jenom jeden objekt konfigurace registrace zařízení. Tato funkce není kompatibilní s topologií, ve které je místní služba Active Directory synchronizovaná s více adresáři služby Azure AD.</li>

## <a name="part-1-install-azure-ad-connect"></a>Část 1: instalace Azure AD Connect
Nainstalujte Azure AD Connect pomocí vlastních nebo expresních nastavení. Společnost Microsoft doporučuje zahájit synchronizaci všech uživatelů a skupin předtím, než povolíte zpětný zápis zařízení.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Část 2: povolení zpětného zápisu zařízení v Azure AD Connect
1. Spusťte Průvodce instalací znovu. Na stránce další úlohy vyberte **Konfigurovat možnosti zařízení** a klikněte na **Další**. 

    ![Konfigurace možností zařízení](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > Nové možnosti konfigurace zařízení jsou k dispozici pouze ve verzi 1.1.819.0 a novější.

2. Na stránce Možnosti zařízení vyberte **Konfigurovat zpětný zápis zařízení**. Možnost **Zakázat zpětný zápis zařízení** nebude k dispozici, dokud nebude povolen zpětný zápis zařízení. Kliknutím na tlačítko **Další** přejdete na další stránku v průvodci.
    ![Volba operace zařízení](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Na stránce zpětný zápis se zobrazí zadaná doména jako výchozí doménová struktura pro zpětný zápis zařízení.
   ![Cílová doménová struktura pro zpětný zápis zařízení vlastní instalace](./media/how-to-connect-device-writeback/writebackforest.png)

4. Stránka **kontejner zařízení** nabízí možnost připravit službu Active Directory pomocí jedné ze dvou dostupných možností:

    a. **Zadejte přihlašovací údaje podnikového správce**: Pokud jsou k dispozici přihlašovací údaje správce podniku pro doménovou strukturu, ve které se musí zařízení zapsat zpátky, Azure AD Connect se automaticky připraví doménová struktura během konfigurace zpětného zápisu zařízení.

    b. **Stažení skriptu PowerShellu**: Azure AD Connect automaticky vygeneruje skript prostředí PowerShell, který může připravit službu Active Directory pro zpětný zápis zařízení. V případě, že se v Azure AD Connect nedají zadat přihlašovací údaje správce podniku, navrhne se stažení skriptu PowerShellu. Poskytněte staženému skriptu PowerShellu **CreateDeviceContainer.ps1** pro podnikového správce doménové struktury, kde se budou zařízení zapisovat zpátky.
    ![Příprava doménové struktury služby Active Directory](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    Pro přípravu doménové struktury služby Active Directory se provádí následující operace:
    * Pokud ještě neexistují, vytvoří a nakonfiguruje nové kontejnery a objekty v části CN = Device Registration Configuration, CN = Services, CN = Configuration, [doménová struktura-DN].
    * Pokud ještě neexistují, vytvoří a nakonfiguruje nové kontejnery a objekty v CN = RegisteredDevices, [Domain-DN]. V tomto kontejneru se vytvoří objekty zařízení.
    * Nastaví potřebná oprávnění pro účet konektoru Azure AD, aby bylo možné spravovat zařízení ve službě Active Directory.
    * Musí běžet jenom v jedné doménové struktuře, a to i v případě, že se Azure AD Connect instaluje do několika doménových struktur.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Ověřit, jestli jsou zařízení synchronizovaná se službou Active Directory
Zpětný zápis zařízení by teď měl správně fungovat. Uvědomte si, že může trvat až 3 hodiny, než se objekty zařízení zapíší zpátky do AD.  Pokud chcete ověřit, že se zařízení synchronizují správně, proveďte následující kroky po dokončení pravidel synchronizace:

1. Spusťte Centrum správy služby Active Directory.
2. V doméně, která je federované, rozbalte RegisteredDevices.

   ![Zaregistrovaná zařízení centra pro správu služby Active Directory](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Tady se zobrazí aktuální registrovaná zařízení.

   ![Seznam registrovaných zařízení centra pro správu služby Active Directory](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Povolit podmíněný přístup
Podrobné pokyny pro povolení tohoto scénáře jsou k dispozici v rámci [nastavení místního podmíněného přístupu pomocí Azure Active Directory Device Registration](../devices/overview.md).

## <a name="troubleshooting"></a>Řešení potíží
### <a name="the-writeback-checkbox-is-still-disabled"></a>Zaškrtávací políčko zpětného zápisu je pořád zakázané.
Pokud políčko pro zpětný zápis zařízení není povolené, i když jste postupovali podle výše uvedených kroků, následující kroky vás provedou tím, co Průvodce instalací ověří před tím, než je box povolený.

První věcí:

* Doménová struktura, kde jsou zařízení přítomná, musí mít schéma doménové struktury upgradované na úroveň Windows 2012 R2, aby byl k dispozici objekt zařízení a přidružené atributy.
* Pokud je již spuštěn Průvodce instalací, nebudou zjištěny žádné změny. V takovém případě dokončete Průvodce instalací a znovu ho spusťte.
* Ujistěte se, že účet, který zadáte do inicializačního skriptu, je ve skutečnosti správným uživatelem používaným konektorem služby Active Directory. K ověření použijte následující postup:
  * V nabídce Start otevřete **synchronizační službu**.
  * Otevřete kartu **konektory** .
  * Najděte konektor s typem Active Directory Domain Services a vyberte ho.
  * V části **Akce** vyberte **vlastnosti**.
  * Přejít na **připojení k doménové struktuře služby Active Directory**. Ověřte, že doména a uživatelské jméno zadané na této obrazovce odpovídají účtu, který jste zadali ke skriptu.
    ![Účet konektoru v synchronizaci Service Manager](./media/how-to-connect-device-writeback/connectoraccount.png)

Ověřit konfiguraci ve službě Active Directory:

* Ověřte, že je služba Device Registration Service umístěná v níže uvedeném umístění (CN = DeviceRegistrationService, CN = Device Registration Services, CN = Device Registration Configuration, CN = Services, CN = Configuration) v části názvový kontext konfigurace.

![Řešení potíží, DeviceRegistrationService v oboru názvů konfigurace](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Prohledáním oboru názvů konfigurace ověřte, zda je k dispozici pouze jeden objekt konfigurace. Pokud existuje více než jeden, odstraňte duplicitní.

![Řešení potíží, hledání duplicitních objektů](./media/how-to-connect-device-writeback/troubleshoot2.png)

* V objektu služby Device Registration Service se ujistěte, že je přítomen atribut msDS-DeviceLocation a má hodnotu. Vyhledá toto umístění a zajistěte, aby byl přítomen s typem objectType msDS-DeviceContainer.

![Řešení potíží s msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Řešení potíží, třída objektů RegisteredDevices](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Ověřte, že účet používaný konektorem služby Active Directory má požadovaná oprávnění pro kontejner registrovaných zařízení, který našel předchozí krok. Toto jsou očekávaná oprávnění pro tento kontejner:

![Řešení potíží, ověření oprávnění u kontejneru](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Ověřte, zda má účet služby Active Directory oprávnění pro objekt CN = Device Registration Configuration, CN = Services, CN = Configuration Object.

![Řešení potíží, ověření oprávnění pro konfiguraci registrace zařízení](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Další informace
* [Řízení rizik pomocí podmíněného přístupu](../conditional-access/overview.md)
* [Nastavení místního podmíněného přístupu pomocí Azure Active Directory Device Registration](../devices/overview.md)

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
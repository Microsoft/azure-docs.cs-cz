---
title: Azure AD Connect náhodné odstranění cloudové synchronizace
description: Toto téma popisuje, jak pomocí funkce náhodného odstranění zabránit odstranění.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785173"
---
# <a name="accidental-delete-prevention"></a>Prevence náhodného odstranění

Následující dokument popisuje funkci náhodného odstranění Azure AD Connect synchronizace cloudu.  Funkce náhodného odstranění je navržená tak, aby vás chránila před nechtěnými změnami konfigurace a změnami v místním adresáři, které by mohly ovlivnit mnoho uživatelů a skupin.  Tato funkce umožňuje:

- Nakonfigurujte schopnost zabránit náhodným odstraněním automaticky. 
- Nastavit počet objektů (prahová hodnota), po jejichž uplynutí bude konfigurace platit 
- Nastavte e-mailovou adresu oznámení tak, aby se po tom, co je příslušná úloha synchronizace v karanténě pro tento scénář, dostala e-mailové oznámení. 

Pokud chcete tuto funkci použít, nastavte prahovou hodnotu pro počet objektů, které se při odstranění mají zastavit.  Takže pokud se toto číslo dosáhne, synchronizace se zastaví a pošle se oznámení na zadaný e-mail.  Toto oznámení vám umožní prozkoumat, co se chystá.


## <a name="configure-accidental-delete-prevention"></a>Konfigurace prevence náhodného odstranění
Chcete-li použít novou funkci, postupujte podle následujících kroků.


1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
2.  Vyberte **Azure AD Connect**.
3.  Vyberte **Spravovat cloudovou synchronizaci**.
4. V části **Konfigurace** vyberte svou konfiguraci.
5. V části **Nastavení** zadejte následující:
    - **E-mail s oznámením** – e-mail použitý k oznámením
    - **Zabránit nechtěnému odstranění** – zaškrtněte toto políčko, pokud chcete funkci povolit.
    - **Prahová hodnota náhodného odstranění** – zadejte počet objektů, pro které se má zastavit synchronizace, a odešle oznámení.

![Náhodné odstranění](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>Obnovování z náhodné instance odstranění
Pokud se setkáte s náhodným odstraněním, zobrazí se stav vaší konfigurace zřizovacího agenta.  Vysloví se **překročení prahové hodnoty pro odstranění**.
 
![Stav náhodného odstranění](media/how-to-accidental-deletes/delete-1.png)

Kliknutím na **překročení prahové hodnoty odstranění** zobrazíte informace o stavu synchronizace.  Tím zobrazíte další podrobnosti. 
 
 ![Stav synchronizace](media/how-to-accidental-deletes/delete-2.png)

Kliknutím pravým tlačítkem myši na tři tečky zobrazíte následující možnosti:
 - Zobrazit protokol zřizování
 - Zobrazit agenta
 - Povolení odstranění

 ![Kliknutí pravým tlačítkem myši](media/how-to-accidental-deletes/delete-3.png)

Pomocí **Zobrazit protokol zřizování** můžete zobrazit položky **StagedDelete** a zkontrolovat informace, které jsou k dispozici na uživatelích, kteří byli odstranění.
 
 ![Protokoly zřizování](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>Povolení odstranění

Akce **povolující** odstranění odstraní objekty, které aktivovaly prahovou hodnotu náhodného odstranění.  Odstranění potvrďte pomocí následujícího postupu.  

1. Klikněte pravým tlačítkem na tři tečky a vyberte možnost **Zakázat odstranění**.
2. Kliknutím na tlačítko **Ano** u potvrzení povolte odstranění.
 
 ![Ano při potvrzení](media/how-to-accidental-deletes/delete-4.png)

3. Zobrazí se potvrzení, že odstranění bylo přijato a stav se vrátí do stavu v pořádku s následujícím cyklem. 
 
 ![Přijmout odstranění](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>Odmítání odstranění

Pokud nechcete odstranění povolit, musíte provést následující akce:
- prozkoumat zdroj odstranění
- vyřešení problému (příklad: organizační jednotka byla omylem přesunuta z oboru a Vy jste ji teď znovu přidali zpět do oboru)
- Spustit **restart synchronizace** v konfiguraci agenta

## <a name="next-steps"></a>Další kroky 

- [Azure AD Connect řešení potíží se synchronizací cloudu?](how-to-troubleshoot.md)
- [Azure AD Connect kódů chyb synchronizace cloudu](reference-error-codes.md)
 


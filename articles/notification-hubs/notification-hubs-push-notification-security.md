---
title: Model zabezpečení centra oznámení
description: Přečtěte si o modelu zabezpečení pro Centra oznámení Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: b871775bc7a6d795e86147ae9cffa27bdd2f3348
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263757"
---
# <a name="notification-hubs-security"></a>Zabezpečení centra oznámení

## <a name="overview"></a>Přehled

Toto téma popisuje model zabezpečení centra oznámení Azure.

## <a name="shared-access-signature-security"></a>Zabezpečení sdíleného přístupového podpisu

Centra oznámení implementují schéma zabezpečení na úrovni entity nazývané *sdílený přístupový podpis* (SAS). Každé pravidlo obsahuje název, hodnotu klíče (sdílený tajný klíč) a sadu práv, jak je vysvětleno dále v [deklaracích zabezpečení](#security-claims). 

Při vytváření centra se automaticky vytvoří dvě pravidla: jedno s právy **naslouchání** (které používá klientská aplikace) a jedno se **všemi** právy (která back-end aplikace používá):

- **DefaultListenSharedAccessSignature**: uděluje pouze oprávnění **k naslouchání.**
- **DefaultFullSharedAccessSignature**: uděluje oprávnění **Naslouchání**, **Správa**a **Odeslat.** Tyto zásady se mají používat pouze v back-endu aplikace. Nepoužívejte jej v klientských aplikacích; používat zásady pouze s **přístupem k naslouchání.** Chcete-li vytvořit nové vlastní zásady přístupu s novým tokenem SAS, přečtěte si [článek Tokeny SAS pro zásady přístupu](#sas-tokens-for-access-policies) dále v tomto článku.

Při provádění správy registrace z klientských aplikací, pokud informace odeslané prostřednictvím oznámení nejsou citlivé (například aktualizace počasí), běžným způsobem přístupu k Centru oznámení je poskytnout klíčhodnotu pravidla přístup pouze pro naslouchání do klientské aplikace, a poskytnout hodnotu klíče pravidla plný přístup k back-endu aplikace.

Aplikace by neměly vkládat hodnotu klíče do klientských aplikací pro Windows Store. místo toho mají klientská aplikace načíst z back-endu aplikace při spuštění.

Klíč s **přístupem k naslouchání** umožňuje klientské aplikaci zaregistrovat se pro libovolnou značku. Pokud vaše aplikace musí omezit registrace na konkrétní značky na konkrétní klienty (například když značky představují ID uživatelů), musí back-end aplikace provést registrace. Další informace naleznete v [tématu Správa registrace](notification-hubs-push-notification-registration-management.md). Všimněte si, že tímto způsobem klientská aplikace nebude mít přímý přístup k centra oznámení.

## <a name="security-claims"></a>Nároky na zabezpečení

Podobně jako u jiných entit jsou operace Centra oznámení povoleny pro tři deklarace zabezpečení: **Naslouchání**, **Odesílání**a **Správa**.

| Deklarovat   | Popis                                          | Operace povoleny |
| ------- | ---------------------------------------------------- | ------------------ |
| Naslouchat  | Vytvořit/aktualizovat, číst a odstranit jednotlivé registrace | Vytvořit nebo aktualizovat registraci<br><br>Čtení registrace<br><br>Čtení všech registrací popisovače<br><br>Odstranit registraci |
| Odeslat    | Odesílání zpráv do centra oznámení                | Odeslat zprávu |
| Správa  | Cruds na oznamovacích centrech (včetně aktualizace přihlašovacích údajů služby PNS a klíčů zabezpečení) a čtení registrací na základě značek |Vytvořit/aktualizovat/číst/odstraňovat centra<br><br>Čtení registrací podle značky |

Centra oznámení přijímají tokeny SAS generované pomocí sdílených klíčů nakonfigurovaných přímo v centru.

Není možné odeslat oznámení do více než jednoho oboru názvů. Obory názvů jsou logické kontejnery pro centra oznámení a nejsou zapojeny do odesílání oznámení.

Pro operace na úrovni oboru názvů používejte zásady přístupu na úrovni oboru názvů. například: výpis rozbočovačů, vytváření nebo mazání rozbočovačů atd. Pouze zásady přístupu na úrovni centra umožňují odesílat oznámení.

### <a name="sas-tokens-for-access-policies"></a>Tokeny SAS pro zásady přístupu

Chcete-li vytvořit novou deklaraci zabezpečení nebo zobrazit existující klíče SAS, postupujte takto:

1. Přihlaste se k portálu Azure.
2. Vyberte **Všechny prostředky**.
3. Vyberte název centra oznámení, pro které chcete vytvořit deklaraci nebo zobrazit klíč SAS.
4. V levé nabídce vyberte **možnost Zásady přístupu**.
5. Chcete-li vytvořit novou deklaraci zabezpečení, vyberte **možnost Nová zásada.** Pojmenujte zásadu a vyberte oprávnění, která chcete udělit. Pak vyberte **OK**.
6. Úplný připojovací řetězec (včetně nového klíče SAS) se zobrazí v okně Zásady přístupu. Tento řetězec můžete zkopírovat do schránky pro pozdější použití.

Chcete-li extrahovat klíč SAS z určité zásady, vyberte tlačítko **Kopírovat** vedle zásady obsahující požadovaný klíč SAS. Vložte tuto hodnotu do dočasného umístění a zkopírujte klíčovou část připojovacího řetězce SAS. Tento příklad používá obor názvů Notification Hubs s názvem **mytestnamespace1**a zásadu s názvem **policy2**. Klíč SAS je hodnota blízká konci řetězce určenému **službou SharedAccessKey**:

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![Získání klíčů SAS](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Další kroky

- [Přehled center oznámení](notification-hubs-push-notification-overview.md)

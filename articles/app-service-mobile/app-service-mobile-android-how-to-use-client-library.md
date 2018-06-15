---
title: Jak používat Azure Mobile Apps SDK pro Android | Microsoft Docs
description: Jak používat Azure Mobile Apps SDK pro Android
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: crdun
ms.openlocfilehash: d89aa308ab8f6684cebbec49bbefdcb54d77c886
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869712"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Jak používat Azure Mobile Apps SDK pro Android

Tento průvodce vám ukáže, jak používat klienta Android SDK pro Mobile Apps k implementaci běžné scénáře, jako například:

* Dotazování na data (vložení, aktualizace a odstranění).
* Ověřování.
* Zpracování chyb.
* Vlastní nastavení klienta.

Tato příručka se zaměřuje na straně klienta SDK pro Android.  Další informace o serverové sady SDK pro Mobile Apps naleznete v tématu [pracovat s .NET back-end SDK][10] nebo [použití back-end Node.js SDK][11].

## <a name="reference-documentation"></a>Referenční dokumentace

Můžete najít [referenční dokumentace rozhraní API Javadocs] [ 12] Android klientské knihovny na Githubu.

## <a name="supported-platforms"></a>Podporované platformy

Azure Mobile Apps SDK pro Android podporuje rozhraní API úrovně 19 až 24 (KitKat prostřednictvím cukrovinkách typu nugát) pro telefon i tablet velikostem.  Ověřování, zejména využívá běžně webové framework získat přihlašovací údaje.  Ověřování serveru toku nefunguje s malé formuláře Multi-Factor zařízení, jako jsou sleduje.

## <a name="setup-and-prerequisites"></a>Instalační program a požadavky

Dokončení [využít postup rychlého spuštění Mobile Apps](app-service-mobile-android-get-started.md) kurzu.  Tato úloha zajistí, že byly splněny všechny požadavky pro Azure Mobile Apps pro vývoj.  Rychlý Start také vám pomůže nakonfigurovat svůj účet a vytvoření vaší první back-endu mobilní aplikace.

Pokud se rozhodnete není k dokončení tohoto kurzu rychlý start, proveďte následující úlohy:

* [Vytvořte back-end mobilní aplikace] [ 13] pro použití s aplikací systému Android.
* V nástroji Android Studio [aktualizace Gradle sestavení souborů](#gradle-build).
* [Povolit oprávnění internet](#enable-internet).

### <a name="gradle-build"></a>Aktualizovat soubor sestavení Gradle

Obě změnit **build.gradle** soubory:

1. Přidejte tento kód, který *projektu* úroveň **build.gradle** souboru uvnitř *buildscript* značky:

    ```text
    buildscript {
        repositories {
            jcenter()
        }
    }
    ```

2. Přidejte tento kód, který *modulu aplikace* úroveň **build.gradle** souboru uvnitř *závislosti* značky:

    ```text
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Nejnovější verze je aktuálně 3.4.0. Podporované verze jsou uvedeny [na panelu koše][14].

### <a name="enable-internet"></a>Povolit oprávnění internet

Pro přístup k Azure, vaše aplikace musí mít povolené oprávnění Internetu. Pokud ještě není povolené, přidejte následující řádek kódu do vaší **AndroidManifest.xml** souboru:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Umožňuje vytvořit připojení klienta

Azure Mobile Apps poskytuje čtyři funkce pro mobilní aplikace:

* Přístup k datům a Offline synchronizace s služby mobilní aplikace Azure.
* Volání rozhraní API vlastní napsané pomocí Azure Mobile Apps Server SDK.
* Ověřování pomocí služby Azure App Service ověřování a autorizace.
* Registrace nabízených oznámení v Notification Hubs.

Každá z těchto funkcí vyžaduje nejprve vytvoření `MobileServiceClient` objektu.  Pouze jeden `MobileServiceClient` objekt by měl být vytvořen v rámci vašeho mobilního klienta (to znamená, musí být Singleton vzor).  Chcete-li vytvořit `MobileServiceClient` objektu:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

`<MobileAppUrl>` Je řetězec nebo objekt adresy URL, která odkazuje na vaše mobilní back-end.  Pokud používáte Azure App Service k hostování vaší mobilní back-end, pak se ujistěte, můžete použít zabezpečené `https://` verze adresy URL.

Klient taky vyžaduje přístup k aktivity nebo kontextu - `this` parametr v příkladu.  Měly by během proběhnout konstrukce MobileServiceClient `onCreate()` metoda aktivity odkazuje v `AndroidManifest.xml` souboru.

Jako osvědčený postup by měl abstraktní komunikace serveru do vlastní třídy (singleton-pattern).  V takovém případě by měla předávat aktivitu v rámci konstruktoru správně nakonfigurovat službu.  Příklad:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Nyní můžete volat `AzureServiceAdapter.Initialize(this);` v `onCreate()` metoda hlavní činnosti.  Žádné jiné metody potřebovali mít přístup k použití klienta `AzureServiceAdapter.getInstance();` získat odkaz na službu adaptéru.

## <a name="data-operations"></a>Operace s daty

Základní sady Azure Mobile Apps SDK je poskytnout přístup k datům uloženým v rámci SQL Azure na back-end mobilní aplikace.  Můžete přístup k těmto datům pomocí silného typu třídy (doporučeno) nebo netypová dotazy (nedoporučuje se).  Hromadným Tato část se zabývá pomocí třídy silného typu.

### <a name="define-client-data-classes"></a>Definování datových tříd, klienta

Pro přístup k datům z tabulek SQL Azure, definujte klienta datových tříd, které odpovídají na tabulky v back-end mobilní aplikace. Příklady v tomto tématu předpokládat tabulku s názvem **MyDataTable**, který má následující sloupce:

* id
* Text
* Dokončení

Objekt odpovídající typu klienta se nachází v souboru s názvem **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Přidejte metody getter a setter metody pro každé pole, které přidáte.  Pokud SQL Azure tabulka obsahuje více sloupců, přidáte by odpovídající pole pro tuto třídu.  Například pokud DTO sloupec s prioritou celé číslo bylo (objekt přenosu dat) a pak může přidejte toto pole, společně s její metody getter a setter metody:

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Další postup vytvoření dalších tabulek v váš back-end Mobile Apps naleznete v tématu [postupy: definování řadič tabulky][15] (.NET back-end) nebo [definovat tabulky pomocí dynamické schématu][16] (back-end Node.js).

Tabulce back-end Azure Mobile Apps definuje pět speciální pole, čtyři, které jsou dostupné klientům:

* `String id`: Globálně jedinečné ID záznamu.  Jako osvědčený postup, ujistěte se, id řetězcovou reprezentaci [UUID] [ 17] objektu.
* `DateTimeOffset updatedAt`: Datum a čas poslední aktualizace.  Pole updatedAt nastavena serverem a musí být nastavena nikdy váš klientský kód.
* `DateTimeOffset createdAt`: Datum a čas vytvořený objekt.  Pole createdAt nastavena serverem a musí být nastavena nikdy váš klientský kód.
* `byte[] version`: Obvykle vyjádřený jako řetězec, verze je také nastavená serverem.
* `boolean deleted`: Označuje, že záznam má byla odstraněna ale ještě nebyla odstraněna.  Nepoužívejte `deleted` jako vlastnost v třídě.

`id` Pole je povinné.  `updatedAt` Pole a `version` pole se používají pro offline synchronizace (pro přírůstkové synchronizace a dojde ke konfliktu řešení v uvedeném pořadí).  `createdAt` Pole je odkaz na pole a není používán klienta.  Názvy jsou názvy "přes přenosu" vlastnosti a nejsou upravit.  Můžete však vytvořit mapování mezi objektu a názvy "přes přenosu" pomocí [gson] [ 3] knihovny.  Příklad:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Vytvořit odkaz na tabulku

Chcete-li získat přístup k tabulce, nejprve vytvořit [MobileServiceTable] [ 8] objekt voláním **jít** metodu [MobileServiceClient][9].  Tato metoda má dva přetížení:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

V následujícím kódu **mClient** je odkaz na MobileServiceClient objektu.  První přetížení se používá, kde název třídy a název tabulky jsou stejné, a ten, používá se v rychlé spuštění:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Druhý přetížení se používá při liší od názvu třídy název tabulky: první parametr je název tabulky.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Dotazování tabulky back-end

Nejprve získejte odkaz na tabulku.  Potom spusťte dotaz na odkaz na tabulku.  Dotaz je libovolnou kombinaci:

* A `.where()` [klauzuli filtru](#filtering).
* `.orderBy()` [Řazení klauzule](#sorting).
* A `.select()` [klauzule výběr pole](#selection).
* A `.skip()` a `.top()` pro [stránkovaného výsledky](#paging).

Klauzulích musí být uvedené v předchozí.

### <a name="filter"></a> Filtrování výsledků

Obecná forma dotazu je:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

V předchozím příkladu vrací všechny výsledky (až maximální velikost stránky nastavená serverem).  `.execute()` Metoda provede daný dotaz na back-end.  Dotaz je převést na [OData v3] [ 19] Dotázat se před přenosem na back-end mobilní aplikace.  Back-end mobilní aplikace na přijetí, převede dotaz na příkazu SQL před provedením v instanci SQL Azure.  Vzhledem k tomu, že nějakou dobu, trvá síťové aktivity `.execute()` metoda vrátí [ `ListenableFuture<E>` ] [ 18].

### <a name="filtering"></a>Filtr vrátil data

Provádění následující dotaz vrátí všechny položky z **ToDoItem** tabulky kde **dokončení** rovná **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** se odkaz na tabulku mobilní službu, která jsme vytvořili dříve.

Definujte filtr pomocí **kde** volání metody na odkaz na tabulku. **Kde** metoda následuje **pole** metoda následuje metodu, která určuje logické predikátu. Zahrnout možných metod predikátem **eq** (rovná), **ne** (nerovná), **gt** (větší než), **ge** (větší než nebo rovno), **lt** (méně než), **le** (je menší než nebo rovno). Tyto metody umožňují porovnat řetězec a číslo pole na konkrétní hodnoty.

Můžete filtrovat podle data. Následující metody umožňují porovnat pole pro celý datum nebo částí data: **roku**, **měsíc**, **den**, **hodinu**, **minutu**, a **druhý**. Následující příklad přidá filtr pro položky jejichž *datum splatnosti* rovná 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Následující metody podporují komplexní filtry na polí s řetězcem: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **nahradit**, **toLower**, **toUpper**, **trim**, a **délka**. Následující příklad filtry pro tabulku řádků, kde *text* sloupec začíná "PRI0."

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Následující operátor metody jsou podporovány na pole s počtem: **přidat**, **sub**, **mul**, **div**, **mod**, **podlaží**, **mezní hodnoty**, a **ZAOKROUHLIT**. Následující příklad filtry pro tabulku řádků, kde **doba trvání** je číslo sudé.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Predikáty můžete kombinovat s tyto logické metody: **a**, **nebo** a **není**. Následující příklad kombinuje dva v předchozích příkladech.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Skupiny a vnořit logické operátory:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Podrobnější diskuzi a příklady filtrování, najdete v části [zkoumat bohatost modelu dotazu Android klienta][20].

### <a name="sorting"></a>Řazení vrátil data

Následující kód vrátí všechny položky z tabulky **ToDoItems** seřadit vzestupně podle *text* pole. *mToDoTable* se odkaz na tabulku back-end, který jste vytvořili dříve:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

První parametr **orderBy** metoda je stejný jako název pole, ve kterém se má seřadit řetězec. Druhý parametr používá **QueryOrder** výčtu k určení, jestli se má seřadit vzestupně nebo sestupně.  Filtrování pomocí ***kde*** metody ***kde*** metoda musí být volána před ***orderBy*** metoda.

### <a name="selection"></a>Vyberte konkrétní sloupce

Následující kód ukazuje, jak vrátit všechny položky z tabulky **ToDoItems**, ale zobrazuje jenom **dokončení** a **text** pole. **mToDoTable** se odkaz na tabulku back-end, která jsme vytvořili dříve.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Parametry vyberte funkce jsou řetězec názvy sloupců v tabulce, které chcete vrátit.  **Vyberte** musí postupovat podle metody, třeba metoda **kde** a **orderBy**. Může následovat stránkování metody, třeba **přeskočit** a **horní**.

### <a name="paging"></a>Vrátit data na stránkách

Data jsou **vždy** vrátil na stránkách.  Maximální počet záznamů vrácených nastavena serverem.  Pokud klient požaduje další záznamy, server vrátí maximální počet záznamů.  Ve výchozím nastavení je maximální velikost stránky na serveru 50 záznamů.

V prvním příkladu ukazuje, jak vybrat prvních pět položek z tabulky. Dotaz vrátí položky z tabulky **ToDoItems**. **mToDoTable** se odkaz na tabulku back-end, který jste vytvořili dříve:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Zde je dotaz, který přeskočí první pěti položek a vrátí další pět:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Pokud chcete získat všechny záznamy v tabulce, implementaci kódu Iterujte přes všechny stránky:

```java
List<MyDataModel> results = new List<MyDataModel>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Žádost o pro všechny záznamy pomocí této metody vytvoří minimálně dva požadavky na back-end mobilní aplikace.

> [!TIP]
> Volba velikosti pravá stránka je rovnováhu mezi využití paměti při žádosti se děje, využití šířky pásma a zpoždění při přijímání dat úplně.  Výchozí hodnota (50 záznamy) je vhodná pro všechna zařízení.  Pokud provozujete výhradně na větší paměti zařízení, zvýšit až 500.  Našli jsme, zvýšení velikosti stránky nad rámec 500 zaznamenává výsledky v zpožděním a velké paměti problémy.

### <a name="chaining"></a>Postupy: řetězení metody dotazů

Může být zřetězen metody použité v dotazování tabulky back-end. Řetězení metody dotazů můžete vybrat konkrétní sloupce filtrované řádků, které jsou seřazené a stránkovaného fondu. Můžete vytvořit komplexní logické filtry.  Každá metoda dotaz vrátí objekt dotazu. Pokud chcete ukončit řady metod a ve skutečnosti spusťte dotaz, volání **provést** metoda. Příklad:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Zřetězené dotazu metody musejí být seřazeny následujícím způsobem:

1. Filtrování (**kde**) metody.
2. Řazení (**orderBy**) metody.
3. Výběr (**vyberte**) metody.
4. stránkování (**přeskočit** a **horní**) metody.

## <a name="binding"></a>Vytvoření vazby dat s uživatelským rozhraním

Datová vazba zahrnuje tři komponenty:

* Zdroj dat
* Na obrazovce rozložení
* Adaptér, který sváže dva společně.

V našem ukázkový kód jsme vrátit data z tabulky Mobile Apps SQL Azure **ToDoItem** na pole. Tato aktivita je běžný vzor pro data aplikací.  Databázové dotazy často vracet kolekci řádků, které klient získá v seznamu nebo pole. V této ukázce je pole zdroje dat.  Kód určuje rozložení obrazovky, který definuje zobrazení dat, která se zobrazí v zařízení.  Dva jsou svázané s společně s adaptér, který tento kód je rozšířením z **ArrayAdapter&lt;ToDoItem&gt;**  třídy.

#### <a name="layout"></a>Definování rozložení

Rozložení je definována několik fragmenty kódu XML. Vzhledem existující rozložení, následující kód představuje **ListView** chceme naplnění našich dat serveru.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

V předchozí kód *listitem* atribut určuje id rozložení pro jednotlivých řádků v seznamu. Tento kód určuje zaškrtávací políčko a jeho přidružené textu a získá instanci jednou pro každou položku v seznamu. Toto rozložení nezobrazí **id** pole a složitější rozložení by zadejte další pole v zobrazení. Tento kód je v **row_list_to_do.xml** souboru.

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>Zadejte adaptér
Vzhledem k tomu, že je zdroj dat naše zobrazení pole **ToDoItem**, jsme podtřídami adaptéru v našem **ArrayAdapter&lt;ToDoItem&gt;**  třídy. Tato podtřídami vytvoří zobrazení pro každý **ToDoItem** pomocí **row_list_to_do** rozložení.  V našem kódu jsme definovali následující třídy, která je rozšířením **ArrayAdapter&lt;E&gt;**  třídy:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Přepsání adaptéry **getView** metoda. Příklad:

```
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

Vytvoříme instance této třídy v našem aktivity následujícím způsobem:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Druhý parametr konstruktoru ToDoItemAdapter je odkaz na rozložení. Nyní jsme můžete vytvořit instanci **ListView** a přiřaďte adaptér, který má **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Adaptér pro vazbu na uživatelské rozhraní

Nyní jste připraveni používat datové vazby. Následující kód ukazuje, jak získat položky v tabulce a výplní místní adaptéru s vrácené položky.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

Volání adaptér vždy, když upravíte **ToDoItem** tabulky. Vzhledem k tomu, že změny se provádí na základě záznamu podle, můžete zpracovat jeden řádek místo kolekce. Když vložíte položku, volání **přidat** metoda na adaptéru; při odstraňování, volání **odebrat** metoda.

Kompletní příklad v můžete najít [projekt Android rychlý Start][21].

## <a name="inserting"></a>Vložení dat do back-end

Vytvoří instanci *ToDoItem* a nastavit jeho vlastnosti.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Potom pomocí **insert()** o vložení objektu:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Odpovídá vrácenou entitu data vložená do tabulky back-end, obsahovala ID a všechny ostatní hodnoty (například `createdAt`, `updatedAt`, a `version` pole) nastavte na back-end.

Mobile Apps tabulky vyžadují sloupec primárního klíče s názvem **id**. Tento sloupec musí být řetězec. Výchozí hodnota ID sloupce je identifikátor GUID.  Můžete zadat další jedinečné hodnoty, například e-mailové adresy nebo uživatelských jmen. Pokud není zadána hodnota ID řetězec vloženého záznamu, back-end generuje nový identifikátor GUID.

Hodnota ID řetězec poskytuje následující výhody:

* ID může být generována bez provedení výměnu zpráv do databáze.
* Záznamy jsou usnadňují sloučení z různých tabulek nebo databází.
* Hodnoty ID lépe integrovat logiku aplikace.

Řetězec ID hodnoty jsou **REQUIRED** pro podporu offline synchronizace.  Id nelze změnit, jakmile je uložen v databázi back-end.

## <a name="updating"></a>Aktualizovat data v mobilní aplikaci

Pokud chcete aktualizovat data v tabulce, předat nový objekt, který má **update()** metoda.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

V tomto příkladu *položky* je odkaz na řádek *ToDoItem* tabulku, která se použila některé změny.  Řádek se stejným **id** se aktualizuje.

## <a name="deleting"></a>Odstranit data v mobilní aplikaci

Následující kód ukazuje, jak k odstranění dat z tabulky zadáním datový objekt.

```java
mToDoTable
    .delete(item);
```

Můžete také odstranit položku zadáním **id** pole řádku odstranit.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Vyhledat konkrétní položky podle Id

Vyhledání položky s konkrétní **id** pole s **lookUp()** metoda:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Postupy: práce s daty bez typu

Netypové programovací model poskytuje přesnou kontrolu nad serializace JSON.  Existují některé běžné scénáře, kde můžete chtít použít bez typu programovací model. Pokud například vaše back-end tabulka obsahuje mnoho sloupců a potřebujete odkazovat na podmnožinu sloupců.  Typové modelu vyžaduje definování všechny sloupce definované v back-end mobilní aplikace v třídě data.  Většina volání rozhraní API pro přístup k datům je podobný typu programovací volání. Hlavní rozdíl je, že v netypové modelu můžete volat metody na **MobileServiceJsonTable** objekt, místo **MobileServiceTable** objektu.

### <a name="json_instance"></a>Vytvoření instance bez typu tabulky

Podobně jako u typu modelu, můžete začít nastavením odkaz na tabulku, ale v takovém případě je **MobileServicesJsonTable** objektu. Získat odkaz na voláním **jít** metoda na instanci klienta:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Po vytvoření instance **MobileServiceJsonTable**, má skoro stejný API, které jsou k dispozici jako s typem programovací model. V některých případech metody trvat bez typu parametru místo typu parametru.

### <a name="json_insert"></a>Vložit do tabulky bez typu
Následující kód ukazuje, jak udělat typu vložení. Prvním krokem je vytvoření [JsonObject][1], který je součástí [gson] [ 3] knihovny.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Poté použijte **insert()** netypové objekt vložit do tabulky.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Pokud potřebujete získat ID vložené objektu, použijte **getAsJsonPrimitive()** metoda.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Odstraňte z bez typu tabulky
Následující kód ukazuje, jak odstranit instance, v takovém případě stejnou instanci **JsonObject** který byl vytvořen v předchozího *vložit* příklad. Kód je stejný jako s typem případ, ale metoda má jiný podpis, protože odkazuje na **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Můžete také odstranit instanci přímo pomocí jeho ID:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Vrátí všechny řádky z tabulky aplikace bez typu
Následující kód ukazuje, jak načíst celou tabulku. Vzhledem k tomu, že používáte JSON tabulky, můžete selektivně načíst jenom některé sloupce v tabulce.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

Stejnou sadu filtrování, filtrování a stránkování metody, které jsou k dispozici pro typové modelu jsou k dispozici bez typu modelu.

## <a name="offline-sync"></a>Implementace Offline synchronizace

Azure Mobile Apps Client SDK také implementuje offline synchronizace dat s použitím databáze SQLite k uložení kopie dat serveru místně.  Operace provedené v offline tabulce nevyžadují mobilní připojení fungovat.  Offline synchronizace je výhodné při odolnost a výkon za cenu složitější logiku pro řešení konfliktů.  Azure Mobile Apps Client SDK implementuje následující funkce:

* Přírůstkové synchronizace: Pouze aktualizované a nové záznamy se stáhnou, ukládání spotřebu šířky pásma a paměti.
* Optimistickou metodu souběžného: Operace se předpokládá, že proběhla úspěšně.  Řešení konfliktů je odložení, dokud nebude aktualizace se provádí na serveru.
* Řešení konfliktů: Sada SDK zjistí, že změna způsobující konflikt byl změněn na serveru a poskytuje háky k upozornění uživatele.
* Obnovitelného odstranění: Odstraněné záznamy jsou označené odstraněné, povolení jiná zařízení k aktualizaci mezipaměti v režimu offline.

### <a name="initialize-offline-sync"></a>Inicializaci Offline synchronizace

Každá tabulka offline musí být definován v mezipaměti offline před použitím.  Za normálních okolností se okamžitě po vytvoření klienta provádí definici tabulky:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Získat odkaz na tabulky mezipaměti v režimu Offline

Pro online tabulky, můžete použít `.getTable()`.  K offline tabulky, použijte `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Všechny metody, které jsou k dispozici pro online tabulky (včetně filtrování, řazení, stránkování, vkládání dat, aktualizace dat a odstraňování dat) pracovat stejně dobře u tabulek se online a offline.

### <a name="synchronize-the-local-offline-cache"></a>Synchronizovat místní mezipaměti v režimu Offline

Synchronizace je v rámci prvku aplikace.  Tady je příklad metoda synchronizace:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Pokud je pro zadaný název dotazu `.pull(query, queryname)` metoda pak přírůstkové synchronizace se používá k vrácení pouze záznamy, které byly vytvořeny nebo změněné od posledního úspěšně dokončit vyžádání obsahu.

### <a name="handle-conflicts-during-offline-synchronization"></a>Zpracování konfliktů během Offline synchronizace

Pokud dojde ke konfliktu při `.push()` operace, `MobileServiceConflictException` je vyvolána výjimka.   Položka server vydal vložené do výjimku, může načíst `.getItem()` na výjimku.  Upravte nabízeného oznámení při volání objektu MobileServiceSyncContext následující položky:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Jakmile se všechny konflikty jsou označené jako nechcete, volání `.push()` znovu a vyřešte všechny konflikty.

## <a name="custom-api"></a>Volání vlastní rozhraní API

Vlastní rozhraní API umožňuje definovat vlastní koncové body, které zveřejňují funkce serveru které není mapovat k typu vložení, aktualizaci, odstranění nebo operace čtení. Pomocí vlastního rozhraní API, může mít větší kontrolu nad zasílání zpráv, včetně čtení a nastavení hlavičky protokolu HTTP zpráv a definování formátu textu zprávy kromě formátu JSON.

V klientovi aplikace Android zavoláte **invokeApi** metoda k volání vlastní koncový bod rozhraní API. Následující příklad ukazuje způsob volání rozhraní API koncový bod s názvem **completeAll**, který vrátí kolekce třídy s názvem **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

**InvokeApi** metoda je volána v klientovi, který odešle požadavek POST do nové vlastní rozhraní API. Výsledek vrácený vlastního rozhraní API zobrazí v dialogu zprávy, jako jsou všechny chyby. Jiné verze **invokeApi** umožňují volitelně odesílat objekt v textu požadavku, zadejte metodu protokolu HTTP a odeslat parametry dotazu s požadavkem. Netypová verze **invokeApi** k dispozici jsou také.

## <a name="authentication"></a>Přidání ověřování do aplikace

Podrobné kurzy již popisují postup přidání těchto funkcí.

App Service podporuje [ověřování uživatelů aplikace](app-service-mobile-android-get-started-users.md) pomocí různých zprostředkovatelů externí identity: Facebook, Google, Microsoft Account, Twitter a Azure Active Directory. Můžete nastavit oprávnění pro tabulky, pokud chcete omezit přístup pro určité operace pouze ověřené uživatele. Můžete také použít identitu ověřeného uživatele k implementaci autorizační pravidla v váš back-end.

Jsou podporovány dva ověřování toky: **server** toku a **klienta** toku. Tok serveru poskytuje nejjednodušší zkušeností ověřování, jako je závislé na webové rozhraní pro zprostředkovatele identity.  Žádné další sady SDK jsou nutné k implementaci tok ověřování serveru. Tok ověřování serveru neposkytuje těsná integrace do mobilního zařízení a doporučuje se pouze pro testování konceptu scénáře.

Tok klienta umožňuje hlubší integrace s funkcí konkrétní zařízení, jako je jednotné přihlašování jako přitom spoléhá na sady SDK od zprostředkovatele identity.  Například můžete integrovat Facebook SDK do své mobilní aplikace.  Mobilního klienta umožňuje přepnout do aplikace Facebook a potvrdí, vaše přihlášení před odkládací zpět do mobilní aplikace.

Čtyři kroky jsou nezbytné pro povolení ověřování v aplikaci:

* Registrace aplikace pro ověřování pomocí zprostředkovatele identity.
* Konfigurace back-end vaší služby App Service.
* Omezte oprávnění tabulka ověřeného uživatele pouze na back-end služby App Service.
* Přidání ověřovacího kódu do vaší aplikace.

Můžete nastavit oprávnění pro tabulky, pokud chcete omezit přístup pro určité operace pouze ověřené uživatele. Identifikátor SID ověřeného uživatele můžete také upravit požadavky.  Další informace najdete v tématu [Začínáme s ověřováním] a v dokumentaci k serveru SDK postupy.

### <a name="caching"></a>Ověřování: Tok serveru

Následující kód spustí proces serveru toku přihlášení pomocí zprostředkovatele Google.  Z důvodu požadavků na zabezpečení u zprostředkovatele Google není nutná další konfigurace:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Kromě toho přidejte následující metodu do hlavní třídy aktivity:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

`GOOGLE_LOGIN_REQUEST_CODE` Definované v vaší hlavní aktivita se používá pro `login()` metoda a v `onActivityResult()` metoda.  Jedinečné číslo, můžete tak dlouho, dokud se v rámci používá stejné číslo `login()` metoda a `onActivityResult()` metoda.  Pokud jste abstraktní kód klienta do služby adaptér (jak je uvedeno výše), by měly volat metody odpovídající na adaptéru služby.

Musíte také nakonfigurovat projekt pro customtabs.  Nejdřív zadejte adresu URL přesměrování.  Přidejte následující fragment k `AndroidManifest.xml`:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Přidat **redirectUriScheme** k `build.gradle` souboru aplikace:

```text
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Nakonec přidejte `com.android.support:customtabs:23.0.1` v seznamu závislostí `build.gradle` souboru:

```text
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.google.code.gson:gson:2.3'
    compile 'com.google.guava:guava:18.0'
    compile 'com.android.support:customtabs:23.0.1'
    compile 'com.squareup.okhttp:okhttp:2.5.0'
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Získání ID přihlášeného uživatele z **MobileServiceUser** pomocí **reprezentuje getUserId** metoda. Příklad použití tříd Future k volání asynchronní přihlášení rozhraní API, naleznete v části [Začínáme s ověřováním].

> [!WARNING]
> Schéma adresy URL uvedené rozlišuje velká a malá písmena.  Ujistěte se, že všechny výskyty `{url_scheme_of_you_app}` malá a velká písmena.

### <a name="caching"></a>Tokeny ověřování do mezipaměti

Ukládání do mezipaměti tokeny ověřování vyžaduje, abyste pro ukládání ID uživatele a ověřovací token místně na zařízení. Při příštím spuštění aplikace, zkontrolujte mezipaměti, a pokud nejsou tyto hodnoty, můžete přeskočit protokolu v postupu a rehydrataci při spotřebě klienta se tato data. Ale tato data jsou citlivé a by měly být uložené šifrována pro zabezpečení v případě, že získá odcizení telefonu.  Zobrazí úplný příklad toho, jak do mezipaměti ověřování tokenů v [mezipaměti část tokeny ověřování][7].

Když se pokusíte použít tokenu vypršela platnost, zobrazí se *401 Neautorizováno* odpovědi. Může zpracovávat ověřování chyb pomocí filtrů.  Filtry zachycení požadavků na back-end služby App Service. Kód filtru testy odpovědi na 401, spustí proces přihlášení a potom obnoví žádost, která generovala kód 401.

### <a name="refresh"></a>Použití obnovovacích tokenů

Token vrácený Azure App Service ověřování a autorizace má definovaná životnosti jednu hodinu.  Po uplynutí této doby musí novému ověření uživatele.  Pokud používáte dlohotrvající token, který jste obdrželi prostřednictvím ověřování tok klienta a pak můžete novému ověření pomocí Azure App Service ověřování a autorizace pomocí jednoho tokenu.  Další token služby Azure App Service je vytvořen s novou životnost.

Můžete také registrovat zprostředkovatele, který má použít aktualizaci tokeny.  Aktualizovat Token není vždy k dispozici.  Je vyžadována další konfigurace:

* Pro **Azure Active Directory**, nakonfigurovat sdílený tajný klíč klienta pro aplikaci Azure Active Directory.  Zadejte sdílený tajný klíč klienta v Azure App Service při konfiguraci ověřování Azure Active Directory.  Při volání metody `.login()`, předat `response_type=code id_token` jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Pro **Google**, předat `access_type=offline` jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Pro **Account Microsoft**, vyberte `wl.offline_access` oboru.

Chcete-li aktualizovat token, volejte `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Jako osvědčený postup vytvoření filtru, který zjistí 401 odpověď ze serveru a pokusí se aktualizovat token uživatele.

## <a name="log-in-with-client-flow-authentication"></a>Přihlaste se pomocí ověřování tok klienta

Obecný postup přihlášení pomocí ověřování tok klienta vypadá takto:

* Konfigurace Azure App Service ověřování a autorizaci, stejně jako server tok ověřování.
* Integrate zprostředkovatele ověřování SDK pro ověřování a vytvořit token přístupu.
* Volání `.login()` metoda následujícím způsobem:

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Nahraďte `onSuccess()` metoda s ať kódu je chcete použít v úspěšném přihlášení.  `{provider}` Řetězec je platný zprostředkovatel: **aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount**, nebo **twitter**.  Pokud jste implementovali vlastní ověřování, můžete také použít poskytovatele značky vlastního ověřování.

### <a name="adal"></a>Ověřování uživatelů pomocí Active Directory Authentication Library (ADAL)

Active Directory Authentication Library (ADAL) můžete použít pro přihlášení uživatelů do vaší aplikace pomocí Azure Active Directory. Pomocí přihlášení toku klienta je často vhodnější než použít `loginAsync()` metody jak poskytuje více nativní UX chování a umožňuje pro další přizpůsobení.

1. Podle konfigurace váš back-end mobilní aplikace při přihlášení AAD [jak nakonfigurovat App Service pro přihlášení služby Active Directory] [ 22] kurzu. Ujistěte se, že dokončení volitelný krok registrace nativní klientskou aplikaci.
2. Nainstalujte ADAL úpravou souboru build.gradle zahrnout následující definice:

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

1. Přidejte následující kód k vaší aplikaci, provedení náhrady následující:

* Nahraďte **INSERT. AUTORITY zde** s názvem klienta, ve kterém jste zřídili vaší aplikace. Musí být ve formátu https://login.microsoftonline.com/contoso.onmicrosoft.com.
* Nahraďte **INSERT-RESOURCE-ID-zde** s ID klienta pro váš back-end mobilní aplikace. Můžete získat ID klienta z **Upřesnit** v části **nastavení Azure Active Directory** na portálu.
* Nahraďte **INSERT klienta ID zde** s ID klienta, který jste zkopírovali z nativní klientskou aplikaci.
* Nahraďte **vložení PŘESMĚROVÁNÍ URI zde** s vaší lokality */.auth/login/done* koncový bod, pomocí schéma HTTPS. Tato hodnota by měla být podobná *https://contoso.azurewebsites.net/.auth/login/done*.

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>Upravit komunikaci klienta se serverem

Připojení klienta je obvykle základní připojení HTTP pomocí základní knihovny HTTP součástí sady SDK pro Android.  Tady je několik důvodů, proč byste měli změnit, který:

* Chcete použít alternativní knihovny HTTP upravit vypršení časových limitů.
* Chcete poskytovat indikátor průběhu.
* Chcete přidat vlastní hlavičku pro podporu funkcí správy rozhraní API.
* Chcete zachytit neúspěšných odpovědí proto, že můžete implementovat opětovné ověření.
* Chcete protokolovat požadavky na back-end do služby analýzy.

### <a name="using-an-alternate-http-library"></a>Pomocí alternativní knihovny HTTP

Volání `.setAndroidHttpClientFactory()` metoda ihned po vytvoření odkaz na klienta.  Chcete-li například nastavit časový limit připojení na 60 sekund (místo výchozího 10 sekund):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClinet();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implementace filtru průběh

Zachycení každou žádost můžete implementovat implementací `ServiceFilter`.  Následující aktualizace například předem vytvořené indikátor průběhu:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    pubic void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Tento filtr je možné připojit klienta následujícím způsobem:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Přizpůsobení hlavičky požadavku

Použijte následující `ServiceFilter` a připojte filtr stejným způsobem jako `ProgressFilter`:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Konfigurace automatického serializace

Můžete zadat převod strategie, která platí pro každý sloupec s použitím [gson] [ 3] rozhraní API. Android Klientská knihovna používá [gson] [ 3] na pozadí a serializovat objekty Java do formátu JSON data předtím, než odešle data do služby Azure App Service.  Následující kód používá **setFieldNamingStrategy()** metoda k nastavení strategie. Tento příklad odstraní počáteční znak ("m") a pak malá další znak, pro každý název pole. Například ho by zapnout "střední" do "id".  Implementace převod strategie pro snížení nároků na `SerializedName()` poznámky na většina polí.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStategy)
);
```

Tento kód musí být spuštěn před vytvořením odkazu mobilního klienta pomocí **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Začínáme s ověřováním]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: http://www.odata.org/documentation/odata-version-3-0/
[20]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html

Teď, když přidáte trigger, s daty, který je generován aktivační událost zajímavé své doby něco udělat. Následujícím postupem přidáte **SFTP - extrahovat složku** akce. Tato akce se extrahovat obsah souboru, pokud jsou splněny podmínky definované. 

Ke konfiguraci této akce, budete muset zadat následující informace. Uvidíte, že je snadno použitelný dat vygenerovaných vašimi aktivační událost jako vstup pro některé vlastnosti nového souboru:

| SFTP - extract vlastnosti složky | Popis |
| --- | --- |
| Zdrojová cesta k souboru archivu |Toto je cesta k souboru extrahují. Můžete vybrat mezi tokeny z předchozích akcí nebo přejděte server SFTP najít cestu k souboru. |
| Cesta k cílové složce |Toto je cesta kde budou umístěné extrahované soubory. Můžete vybrat jeden z tokenů z předchozí akce jako cílovou cestu nebo přejděte server SFTP a vyberte cestu. |
| Přepsat? |Označuje, pokud soubor se stejným názvem jako extrahovaný soubor naleznete ve cesta k cílové složce Pokud má se přepsat existující soubor, nebo ne. |

Pusťme se do práce přidání akce a rozbalte soubory, pokud se vyhodnotí jako podmínka definovali dříve *True*. 

1. Vyberte **přidat akci**.        
   ![Obrázek podmínku akce SFTP 6](./media/connectors-create-api-sftp/condition-6.png)   
2. Vyberte **SFTP - extrahovat složku** akce      
   ![Obrázek podmínku akce SFTP 7](./media/connectors-create-api-sftp/condition-7.png)   
3. Vyberte **cesta ke zdrojovému archivnímu souboru**              
   ![Obrázek podmínku akce SFTP 9](./media/connectors-create-api-sftp/condition-9.png)   
4. Vyberte **cesta k souboru** token. To znamená, že bude používat cestu k souboru, kterou objevila aktivační událost jako cesta ke zdrojovému archivnímu souboru.           
   ![Obrázek podmínku akce SFTP 10](./media/connectors-create-api-sftp/condition-10.png)   
5. Vyberte **cesta k cílové složce**           
   ![Obrázek podmínku akce SFTP 11](./media/connectors-create-api-sftp/condition-11.png)   
6. Vyberte **cesta k souboru** token. To znamená, že použijete cestu k souboru, kterou objevila aktivační událost jako cílovou cestu pro extrahované soubory.   
7. Zadejte *\ExtractedFile* v **cesta k cílové složce** ovládacího prvku. To lze proveďte hned za token cesta k souboru v ovládacím prvku cestu cílové složky.         
   ![Obrázek podmínku akce SFTP 12](./media/connectors-create-api-sftp/condition-12.png)   
8. Zadejte *True* v **přepsat?* ovládacího prvku k označení, že má přepsat existující soubory, pokud mají stejný název jako extrahované soubory.      
   ![Obrázek podmínku akce SFTP 13](./media/connectors-create-api-sftp/condition-13.png)   
9. Uložte změny do svého pracovního postupu  


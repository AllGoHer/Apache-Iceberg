# Apache-Iceberg
____________________________________________________________________________________________________________________________________________________________________________________________________________________

1.	Abrimos docker desktop

![image](https://github.com/user-attachments/assets/bd7ab30d-1206-4756-8c9e-cf5adead022a)

2.	En powershell ejecutamos el siguiente comando para crear la carpeta Iceberg.

Powershell:

      mkdir Iceberg

  ![image](https://github.com/user-attachments/assets/37229a30-e817-4a7f-a331-d7cb9f065634)

3.	Luego ingresamos a la carpeta.

Powershell:

    Cd Iceberg

4.	Creamos las subcarpetas.

Powershell:

     mkdir jars, apps, data, iceberg-warehouse

  ![image](https://github.com/user-attachments/assets/a94f64d6-ff24-4698-b0d7-3090282a6eb5)

Verificamos la creación de las subcarpetas.

  ![image](https://github.com/user-attachments/assets/524a18f6-90a6-4550-9c49-5f36807d133e)

 5.	Luego entra a mi repositorio y descarga docker-compose dentro de la carpeta Iceberg.


Repositorio GitHub: https://github.com/AllGoHer/Apache-Iceberg
 

  ![image](https://github.com/user-attachments/assets/8bc85f33-fb13-4e95-aeda-732bea21e89e)
  

6.	Ahora, en powershell, ejecutamos el siguiente comando de descarga del archivo.

Powershell:

            Invoke-WebRequest ` -Uri "https://repo1.maven.org/maven2/org/apache/iceberg/iceberg-spark-runtime-3.5_2.12/1.9.2/iceberg-spark-runtime-3.5_2.12-1.9.2.jar" `
               -OutFile ".\jars\iceberg-spark-runtime-3.5_2.12-1.9.2.jar"


  ![image](https://github.com/user-attachments/assets/30e96c65-b5be-4e47-9fd9-68c4363456e1)


Ahora, verificamos que el archivo se haya creado en la carpeta jars.

  ![image](https://github.com/user-attachments/assets/89405a95-fa18-41a7-b1f4-189b2afa2255)


7.	Ahora, levantamos docker-compose para crear e inicializar los contenedores.

Powershell:

            docker compose up -d

  ![image](https://github.com/user-attachments/assets/90504941-97e3-4fa5-8ea6-76d3953fc7bc)

Verificamos que se haya creado en docker desktop.

  ![image](https://github.com/user-attachments/assets/fdfc62c2-6cdf-4d21-b6ae-a231af7d73db)

También podemos verificar mediante powershell que se hayan creado y estén activos.

Powershell:

            docker ps


  ![image](https://github.com/user-attachments/assets/98d2b69f-fa15-46f7-801e-3263403d8439)

* Ahora, en esta etapa vamos a configurar Iceberg en Spark. Abriremos una nueva ventana de powershell y ejecutamos el siguiente comando para entrar a una ventana interactiva. 

Powershell:

           docker exec -it iceberg-spark-worker bash


  ![image](https://github.com/user-attachments/assets/bca202f9-cefc-4552-8248-80cbc634b2da)

  Luego, pasamos el comando para crear y abrir una aplicación de Spark con configuraciones

Código:

        /opt/spark/bin/pyspark \
          --master spark://spark-master:7077 \
          --jars /opt/iceberg-jars/iceberg-spark-runtime-3.5_2.12-1.9.2.jar \
          --conf spark.sql.extensions=org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions \
          --conf spark.sql.catalog.local=org.apache.iceberg.spark.SparkCatalog \
          --conf spark.sql.catalog.local.type=hadoop \
          --conf spark.sql.catalog.local.warehouse=/opt/iceberg/warehouse


  ![image](https://github.com/user-attachments/assets/f939f859-1a6b-447e-88db-610602eff42a)


  ### ETAPA DE VALIDACION DE LA INFRAESTRUCTURA - ICEBERG RUNTIME

-  Versión de spark.

Código:

        print(spark.version)

-  Extensiones de spark.

Código:

        print(spark.conf.get("spark.sql.extensions"))


  ![image](https://github.com/user-attachments/assets/911da529-06ca-4349-b6fd-056fd6892928)


- Catalogo local.

Código:

        print(spark.conf.get("spark.sql.catalog.local.type"))



  ![image](https://github.com/user-attachments/assets/6bf758bb-7b42-40b0-af13-d42216e6b2dd)


- Identificador de espacio de nombres.

Código:

spark.sql("""CREATE NAMESPACE IF NOT EXISTS local.demo""")



  ![image](https://github.com/user-attachments/assets/7b69faa7-6875-4887-8b5a-90da149b732f)

  
- Verificamos si se creo es espacio de nombre.

Código:

        spark.sql("""SHOW NAMESPACES IN local""").show()


  ![image](https://github.com/user-attachments/assets/cb30b65e-706a-4c4b-b6f0-3b825b9f9d8a)

  
- Creamos una tabla con nombre cliente.

Código:

        spark.sql("""
        CREATE TABLE local.demo.customers(
             customer_id BIGINT,
             name STRING,
             city STRING
         )
         USING iceberg
         """)


  ![image](https://github.com/user-attachments/assets/e219ad14-7515-47a9-b1b5-426226164527)


- Verificamos la creación de la tabla.

Código:

        Spark.sql("""
         SHOW TABLES IN local.demo
         """).show()



  ![image](https://github.com/user-attachments/assets/d6ed8a01-41e0-4e36-ad65-f01d8d2ae515)

- Verificamos el esquema de la tabla.

Código:

        spark.sql("""
         DESCRIBE TABLE local.demo.customers
         """).show()


  ![image](https://github.com/user-attachments/assets/587f67a8-21c5-4104-89b1-6e24ae971c14)

  - Insertamos valores a la tabla.

Código:

        spark.sql("""
         INSERT INTO local.demo.customers VALUES
             (1, 'Allan', 'Nuevo Chimbote'),
             (2, 'Jose', 'Lima'),
             (3, 'Martin', 'Chimbote')
         """)



  ![image](https://github.com/user-attachments/assets/75ee2e27-e50a-4ed9-a71b-5b9e3df8516c)


Luego, verificamos la creación de valores en la tabla.

Código:

        spark.sql("""
         SELECT *
         FROM local.demo.customers
         """).show()


  ![image](https://github.com/user-attachments/assets/2d3b8775-8578-4753-a8ce-db49b8807a6c)
  
Con esto finalizamos el paso de validación.

### ETAPA DE CATALOGOS DE ICEBERG

Crearemos una tabla llamada products en iceberg

Código:

        spark.sql("""
         CREATE TABLE local.demo.products(
             product_id BIGINT,
             product_name STRING,
             category STRING,
             price DOUBLE
         )
         USING iceberg
         TBLPROPERTIES (
             'write.format.default' = 'avro'
         ) """)


  ![image](https://github.com/user-attachments/assets/326ae98b-5363-4d6a-9579-40330184af8c)
  
- Insertar datos a la tabla products.

Código:

        spark.sql("""
         INSERT INTO local.demo.products VALUES
              (1, "Laptop", "Electronics", 6500.0),
              (2, "Desk", "Furniture", 420.0),
              (3, "CPU", "Electronics", 5400.0)
         """)


  ![image](https://github.com/user-attachments/assets/0143846a-79a3-4fa5-9101-2c14f22509ef)

- Ahora, inspeccionamos la tabla.

Código:

        spark.sql("""
         SELECT *
         FROM local.demo.products
         """).show()


  ![image](https://github.com/user-attachments/assets/c6624da2-0f92-46d3-a2f7-b8f20bfb3338)

  Luego, verificamos si los archivos fueron creados en las subcarpetas.


  ![image](https://github.com/user-attachments/assets/5149d699-d3d3-4627-a8a0-3c87560c3573)


  ![image](https://github.com/user-attachments/assets/f6f191f9-d91a-456f-b978-5ea7cf379e7f)

  - Creando una tabla con un DataFrame

Código:

        data = [
             (1, "Keyboard", "Electronics", 119.90),
             (2, "Ipad", "Electronics", 1799.50),
             (3, "Chair", "Furniture", 888.95)
         ]
        
         df = spark.createDataFrame(
             data,
             ["product_id", "product_name", "category", "price"]
         )
        
         df.writeTo(
              "local.demo.products_df"
         ).create()



  ![image](https://github.com/user-attachments/assets/368e4642-2116-4bcc-86ac-314a8eb4c62e)

  - Inspeccionamos la Tabla

Codigo:

       spark.sql("""
       SELECT *
       FROM local.demo.products_df
       """).show()



  ![image](https://github.com/user-attachments/assets/1e1da41f-458f-42ea-a05c-6cb45c98ff88)

  - Crear una tabla a partir de una consulta — CTAS

Código:

        spark.sql("""
         CREATE TABLE local.demo.product_summary
         USING iceberg
         AS
         SELECT
             category,
             COUNT(*) AS product_count,
             AVG(price) AS average_price
         FROM local.demo.products
         GROUP BY category
         """)



  ![image](https://github.com/user-attachments/assets/59077199-326a-4743-a35a-88e1f81e4500)

  - Inspeccionar tabla

Código:

        spark.sql("""
         SELECT *
         FROM local.demo.product_summary
         """).show()



  ![image](https://github.com/user-attachments/assets/699c310f-829e-4cac-acbe-af1599f96d1e)

- Creación de tabla con particiones.

Código:

        spark.sql("""
         CREATE TABLE local.demo.events (
             event_id BIGINT,
             event_time TIMESTAMP,
             event_type STRING,
             user_id BIGINT
         )
         USING iceberg
         PARTITIONED BY (days(event_time))
         """)



  ![image](https://github.com/user-attachments/assets/e6d09a47-eb0e-4591-90d4-a2a49dc93337)

Insertar datos.

Código:

        spark.sql("""
         INSERT INTO local.demo.events VALUES
             (1, TIMESTAMP '2026-09-18 09:15:00', 'login',    101),
             (2, TIMESTAMP '2026-09-18 10:30:00', 'purchase', 102),
             (3, TIMESTAMP '2026-09-19 08:45:00', 'logout',   103),
             (4, TIMESTAMP '2026-09-19 14:20:00', 'purchase', 104),
             (5, TIMESTAMP '2026-09-20 11:10:00', 'login',    105),
             (6, TIMESTAMP '2026-09-20 16:35:00', 'purchase', 106);
         """)



  ![image](https://github.com/user-attachments/assets/11901ac3-a450-4f49-a8b0-938e2142701f)

Inspeccionamos la tabla.

Código:

        spark.sql("""
         SELECT *
         FROM local.demo.events
         """).show()



  ![image](https://github.com/user-attachments/assets/2240b476-8943-4ddf-a8b2-3c80a8a9dee1)

  Ahora, podemos verificar en las carpetas como se han particionado.


  ![image](https://github.com/user-attachments/assets/a0d36258-f9bf-4c3b-857c-0ddfd3b95d8c)

  

  ![image](https://github.com/user-attachments/assets/e02f205c-000f-41ea-97d7-990f97404ffb)
  


  ![image](https://github.com/user-attachments/assets/6a4bcd02-bec7-4a18-be2c-2f06d217defa)
  
En esta carpeta podemos ver que se han particionado por días.


  ![image](https://github.com/user-attachments/assets/0a89b86e-4ffe-457d-814b-299caa9be928)


### FASE 4 — LECTURA Y ESCRITURA DE TABLAS DE ICEBERG

- Añadir Escritura.

Código:

        spark.sql("""
         INSERT INTO local.demo.customers VALUES
             (4, 'Carlos', 'Trujillo'),
             (5, 'Angie', 'Ica')""")


  ![image](https://github.com/user-attachments/assets/299de12b-e4a3-4447-bc04-2999cae8a12c)
  

- Añadir usando DataFrameWriteV2

Código:

        new_customers = [ (4, 'Carlos', 'Trujillo'),
                           (5, 'Angie', 'Ica')]
         new_customers_df = spark.createDataFrame(
             new_customers,
             ["customer_id", "name", "city"])
         new_customers_df.writeTo("local.demo.customers").append()



  ![image](https://github.com/user-attachments/assets/e62d1cad-b6c2-4489-833b-9ce700dec055)

Ahora, inspeccionamos la tabla.

código:

        spark.sql("""
         SELECT *
         FROM local.demo.customers """).show()



  ![image](https://github.com/user-attachments/assets/e58d43f6-0014-47f4-8c27-85e3901111dc)

- Ahora, vamos a consultar las instantáneas (snapshot) para viajar en el tiempo.

Código:

        spark.sql("""
         SELECT
             snapshot_id,
             parent_id,
             committed_at,
             operation
         FROM local.demo.customers.snapshots
         ORDER BY committed_at
         """).show(truncate=False)



  ![image](https://github.com/user-attachments/assets/ce04013f-4cce-486a-9a3b-1e96079c839b)

Siguiente paso será viajar en el tiempo usando Timestamp

Código:

        # Time Travel Using Timestamp
        
         spark.sql("""
         SELECT *
         FROM local.demo.customers
         TIMESTAMP AS OF '2026-11-19 17:49:48.703'
         """).show(truncate=False)



  ![image](https://github.com/user-attachments/assets/d048c409-fe6a-451a-a991-e14ad7266c4a)

  
* Viaje en el tiempo usando Snapshot ID

Código:

        # Time Travel Using Snapshot ID
        
         spark.sql("""
         SELECT *
         FROM local.demo.customers
         VERSION AS OF 6795951736699684663
         """).show(truncate=False)
         


  ![image](https://github.com/user-attachments/assets/55138bd5-0192-4e4b-95fb-3373bae244fb)

* Inspección de Historial de la tabla

Código:

        # Inspect the Table History
        
         spark.sql("""
         SELECT *
         FROM local.demo.customers.history
         ORDER BY made_current_at
         """).show(truncate=False)



  ![image](https://github.com/user-attachments/assets/bb6ce819-fef3-42e7-a8ee-d6b5e82837a9)

* Regresar la tabla a una instantánea (snapshot) previa.

Código:

        # Roll Back the table to a previous Snapshot
        
         spark.sql("""
         CALL local.system.rollback_to_snapshot(
             table => 'demo.customers',
             snapshot_id => 3571994297089221451
         )
         """).show(truncate=False)



  ![image](https://github.com/user-attachments/assets/85d55a6a-d25f-44d8-99ea-0c37cc48a7ed)

Ahora, volvemos a revisar el historial de la tabla antes del Rollback.

Código:

        # Re-inspect the Table History after Rollback
        
         spark.sql("""
         SELECT *
         FROM local.demo.customers.history
         ORDER BY made_current_at
         """).show(truncate=False)



  ![image](https://github.com/user-attachments/assets/d8e080ef-4eca-4dbf-932b-df67d39ce3ed)

Ahora podemos ver, que el que está marcado como false, ya no es el antecesor del estado actual.

Código:

          # Re-inspect the Available Snapshot
          
           spark.sql("""
           SELECT
               snapshot_id,
               parent_id,
               committed_at,
               operation
           FROM local.demo.customers.snapshots
           ORDER BY committed_at
           """).show(truncate=False)


  ![image](https://github.com/user-attachments/assets/5af8ee6c-a065-4827-9c1e-864511762c59)


Esto demuestra que no se crea un nuevo estado de la tabla, simplemente esta reutilizando el estado anterior.


* Establecer la instantánea de la tabla actual

Código:

        # Set the Current Table Snapshot
        
        spark.sql("""
        CALL local.system.set_current_snapshot(
            table => 'demo.customers',
            snapshot_id => 2036410356086388718
        )
        """).show(truncate=False)


  ![image](https://github.com/user-attachments/assets/0df70c94-207a-4b36-b40a-beccf47aedf0)
  

Ahora, volvemos a imprimir el historial.

Código:

        # Re-inspect the Table History after Setting the Current Snapshot
        
        spark.sql("""
        
        SELECT *
        FROM local.demo.customers.history
        ORDER BY made_current_at
        """).show(truncate=False)


  ![image](https://github.com/user-attachments/assets/a46e4f17-967b-44b8-9a66-d1cebc5e10d7)

* Crear una Tabla de Particionamiento

Código:

        # Create Partitions demo table
        
         spark.sql("""
         CREATE TABLE local.demo.partition_demo
         USING iceberg
         PARTITIONED BY (days(event_time))
         AS
         SELECT *
         FROM local.demo.events
         """)



  ![image](https://github.com/user-attachments/assets/8e33afc0-9ac2-45eb-b399-3eb7dd60e7b7)

* Modificar la partición de una tabla

Código:

        # Alter table partition
        
         spark.sql("""
         ALTER TABLE local.demo.partition_demo
         REPLACE PARTITION FIELD days(event_time)
         WITH hour(event_time)
         """)



  ![image](https://github.com/user-attachments/assets/372237a8-f41a-47de-936d-a460ac0b4dbd)

* Insertar nuevos datos

Código:

        # Insert new data
        
        spark.sql("""
        INSERT INTO local.demo.partition_demo VALUES
            (7, TIMESTAMP '2026-11-20 01:51:23', 'login', 103),
            (8, TIMESTAMP '2026-11-20 01:54:55', 'purchanse', 102),
            (9, TIMESTAMP '2026-11-20 02:02:14', 'logout', 101)""")

  ![image](https://github.com/user-attachments/assets/da51df4c-6b7f-488e-b379-db59e5f8a187)

 Verificamos en la carpeta partition_demo/data  

  ![image](https://github.com/user-attachments/assets/144d5437-efd4-4f32-af5a-074a3a8f2881)

* Crear la tabla inicial Iceberg 

Código:

            # Create the initial Iceberg table
            
             spark.sql("""
             CREATE OR REPLACE TABLE local.demo.mutations_demo(
             id INT,
             name STRING,
             country STRING
             )
             USING iceberg
             """)


  ![image](https://github.com/user-attachments/assets/6c7ecce5-4d75-465d-9ab0-f095905f95f3)

* Insertar datos a la tabla

Código:

            # Insert the initial records
            
             spark.sql("""
             INSERT INTO local.demo.mutations_demo Values
             (1, 'Allan', 'Peru'),
             (2, 'Martin', 'Argentina'),
             (3, 'Karla', 'Bolivia')
             """)


  ![image](https://github.com/user-attachments/assets/012d4dad-3d1c-4069-9a75-87cc5ec9ec14)

* Crea una segunda tabla Iceberg a partir de la original

Código:

            # Create a second Iceberg Table from the original
            
             spark.sql("""
             CREATE OR REPLACE TABLE local.demo.mutations_demo_mor
             USING iceberg
             AS
             SELECT *
             FROM local.demo.mutations_demo
             """)


  ![image](https://github.com/user-attachments/assets/9b3bfbfd-32f1-45ee-a4bd-4bc885334ca7)
  

  ![image](https://github.com/user-attachments/assets/df735889-ceba-4059-8f00-551cc2cac038)

* Actualizar un registro en la tabla original

Código:

            # Update a record in the original table
            
             spark.sql("""
             UPDATE local.demo.mutations_demo
             SET country = 'Peru'
             WHERE id = 2
             """)

  ![image](https://github.com/user-attachments/assets/931dd091-b023-49e5-9bca-93837ea105db)

* Consultar a la tabla para verificar la actualización.

Código:

            # Query the table to verify the update
            
             spark.sql("""
             SELECT *
             FROM local.demo.mutations_demo
             ORDER BY id
             """).show()


  ![image](https://github.com/user-attachments/assets/cb64617b-a91b-4cb8-9017-e2b9ddbfa008)

* Eliminar un registro de la tabla original

Código:

            # DELETE A RECORD FROM THE ORIGINAL TABLE
            
             spark.sql("""
             DELETE FROM local.demo.mutations_demo
             WHERE id = 3
             """)


  ![image](https://github.com/user-attachments/assets/69ba2b54-d266-4697-9fdf-4494fea34024)

* Inspección la tabla instantánea (snapshot) y sus operaciones. 

Código:

            # Inspect the table snapshot and their operations
            
             spark.sql("""
             SELECT
             snapshot_id,
             committed_at,
             operation
             FROM local.demo.mutations_demo.snapshots
             ORDER BY committed_at
             """).show(truncate=False)

  ![image](https://github.com/user-attachments/assets/0c4acfb9-1e0c-46f1-aef3-27e0c7592edb)

* Configura la segunda tabla para que utilice merge-on-read para el formato

Código:

            # Configure the second table to use merge-on-read for format
            
             spark.sql("""
             ALTER TABLE local.demo.mutations_demo_mor
             SET TBLPROPERTIES (
                 'write.delete.mode'='merge-on-read'
             )
             """)


  ![image](https://github.com/user-attachments/assets/32892ca3-cedd-4863-bcc8-9182b6be1aac)

* Actualizar un registro utilizando la tabla de fusión al leer

Código:

            # Update a record using the merge-on-read table
            
             spark.sql("""
             UPDATE local.demo.mutations_demo_mor
             SET country = 'Peru'
             WHERE id = 2
             """)

  ![image](https://github.com/user-attachments/assets/ee9abf99-d437-4a49-beb1-4d853047a6fa)

* Borrar un registro usando la tabla de fusión al leer.

Código:

            # Delete a record using the merge-on read table
            
             spark.sql("""
             DELETE FROM local.demo.mutations_demo_mor
             WHERE id = 3
             """)

  ![image](https://github.com/user-attachments/assets/8ae6c970-fa54-4fce-8fc9-a2ecbb1c212f)

* Revisar los archivos de eliminación generados por la tabla

Código:

            # Inspect the delete files generated by the table
            
             spark.sql("""
             SELECT *
             FROM local.demo.mutations_demo.delete_files
             """).show(truncate=False)



  ![image](https://github.com/user-attachments/assets/f054a84a-6f5b-486e-ba6b-d140ee4ef242)

Como vemos no hay archivos dentro de la tabla para eliminar

* Inspección de la posición de los archivos borrados generados por la tabla de fusión al leer.

Código:

            # Inspect position delete files generated by the merge-on -read table
            
             spark.sql("""
             SELECT *
             FROM local.demo.mutations_demo_mor.position_deletes
             """).show(truncate=False)


  ![image](https://github.com/user-attachments/assets/751e90e0-0e63-4e7d-8bdd-4964571bdf76)

* Inspeccionar las instantáneas de la tabla antes de la expiración 

Código:

            # Inspect the table snapshots before expiration
            
             spark.sql("""
             SELECT
                 snapshot_id,
                 committed_at,
                 operation
             FROM local.demo.mutations_demo_mor.snapshots
             ORDER BY committed_at
             """).show(truncate=False)


  ![image](https://github.com/user-attachments/assets/1a9f121b-c161-40ab-9cc8-34c1fd2f9b36)

*  Eliminar instantáneas más antiguas que la marca de tiempo especificada

Código:

            # Expire snapshots older than the specified tiemstamp
             spark.sql("""
             CALL local.system.expire_snapshots(
                 table => 'demo.mutations_demo_mor',
                 older_than => TIMESTAMP '2026-11-20 05:34:25.587'
             )
             """)


  ![image](https://github.com/user-attachments/assets/f57fa0c6-c218-45ac-b75a-f3ae1e47ee05)

* Revisa las instantáneas restantes después de su vencimiento

Código:

            # Inspect the remaining snapshots after expiration
             spark.sql("""
             SELECT
                 snapshot_id,
                 committed_at,
                 operation
             FROM local.demo.mutations_demo_mor.snapshots
             ORDER BY committed_at
             """).show(truncate=False)


  ![image](https://github.com/user-attachments/assets/d4e30a64-6cc9-489b-9f9b-0f3331221dc5)

* Reescritura de manifiestos

Código:

            # Re-Writing manifests
             spark.sql("""
             CALL local.system.rewrite_manifests(
                 'demo.mutations_demo'
             )""")


  ![image](https://github.com/user-attachments/assets/81871ca1-948f-4392-8720-3c73924dc562)

* Crear datos huérfanos 

Para ello abrimos otra terminal powershell e ingresamos el siguiente código. 

Código:

            docker exec -it iceberg-spark-worker bash
            luego el siguiente código, pero reemplazando <ACTUAL_PARQUET_FILE> por el ultimo archivo creado en la carpeta mutation_demo/data.
            
            Código:
            cp /opt/iceberg/warehouse/demo/mutations_demo/data/<ACTUAL_PARQUET_FILE>.parquet \
               /opt/iceberg/warehouse/demo/mutations_demo/data/orphan-demo.parquet && \
            touch -d "2025-09-20 00:00:00" \
               /opt/iceberg/warehouse/demo/mutations_demo/data/orphan-demo.parquet


  ![image](https://github.com/user-attachments/assets/4be46ff5-0c70-4b87-ba25-41b6e0511166)

* Inspeccionar la tabla instantánea actual

Código:

            # Inspect the current table snapshots
            
             spark.sql("""
             SELECT
                 snapshot_id,
                 committed_at,
                 operation
             FROM local.demo.customers.snapshots
             ORDER BY committed_at
             """).show(truncate=False)


  ![image](https://github.com/user-attachments/assets/a3ff2929-4e7f-4cf4-bae2-bcce6d47de6b)

* Crear una Rama (branch) desde una instantánea especifica.

Código:

            # Create a branch from a specific snapshots
             spark.sql("""
             ALTER TABLE local.demo.customers
             CREATE BRANCH test_branch
             AS OF VERSION 3571994297089221451
             """)


  ![image](https://github.com/user-attachments/assets/98c83884-1ac2-4211-954d-bedf2691f54b)

* Consulta la tabla a través de la rama de prueba

Código:

            # Query the table through the test branch
             spark.sql("""
             SELECT *
             FROM local.demo.customers.branch_test_branch
             """).show(truncate=False)

  ![image](https://github.com/user-attachments/assets/d283e1db-8b53-4bbc-a179-9672a39e1062)

* Borrar un registro desde la rama test

Código:

            # Delete a record from the test branch
             spark.sql("""
             DELETE FROM local.demo.customers.branch_test_branch
             WHERE customer_id = 3
             """)


  ![image](https://github.com/user-attachments/assets/bbc394b9-265e-4367-904b-a91c9adaa114)

* Verificar la instantánea después de la modificación de la rama.

Código:

            # Inspect snapshots after modifying the branch
            
             spark.sql("""
             SELECT
                 snapshot_id,
                 committed_at,
                 parent_id,
                 operation
             FROM local.demo.customers.snapshots
             ORDER BY committed_at
             """).show(truncate=False)


  ![image](https://github.com/user-attachments/assets/52c1f163-a63f-401a-94ff-c554489e077b)

* Revertir la rama principal a una instantánea específica.

Código:

            # Roll back the main branch to a specific snapshot
             spark.sql("""
             CALL local.system.rollback_to_snapshot(
                 'demo.customers',
                 3571994297089221451
             )
             """)


  ![image](https://github.com/user-attachments/assets/43d13d5d-5019-4c95-83c8-f59d79ddd820)

* Actualiza la rama principal a la rama de prueba

Código:

            # Fast-forward the main branch to the test branch
            
             spark.sql("""
             CALL local.system.fast_forward(
                 'demo.customers',
                 'main',
                 'test_branch'
             )
             """)


  ![image](https://github.com/user-attachments/assets/7160afc2-91da-4988-bbfe-ffca5ee3b9ed)

* Verificar la tabla de referencia

Código:

            # Inspect the table reference
            spark.sql("""
            SELECT *
            FROM local.demo.customers.refs
            """).show(truncate=False)


  ![image](https://github.com/user-attachments/assets/1d04f746-c3f6-420f-9f85-5ce55a47410e)

* Consultar el estado actual de la tabla.

Código:

            # Query the current state of the table
             spark.sql("""
             SELECT *
             FROM local.demo.customers
             """).show(truncate=False)


  ![image](https://github.com/user-attachments/assets/0ae2cd23-9f54-4fce-a2eb-9e83acfdcc73)

Aquí podemos observar que se elimino el cliente con Id = 3.


* Crear una etiqueta desde una instancia especifica.

Código:

            # Create a tag from a specific snapshot
             spark.sql("""
             ALTER TABLE local.demo.customers
             CREATE TAG release_v1
             AS OF VERSION 2036410356086388718
             """)


  ![image](https://github.com/user-attachments/assets/a75d9182-389c-4cd1-b30a-f24d392efa72)

* Consultar la tabla usando la etiqueta release_v1.

Código:

            spark.sql("""
            SELECT *
            FROM local.demo.customers
            VERSION AS OF 'release_v1'
            """).show(truncate=False)


  ![image](https://github.com/user-attachments/assets/e41ac43b-48ea-4de6-8446-c54565490862)

  

 

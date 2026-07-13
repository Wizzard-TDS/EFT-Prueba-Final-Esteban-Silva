[Esteban_Silva_EFT4.py](https://github.com/user-attachments/files/29984874/Esteban_Silva_EFT4.py)
# URL del repositorio GIT: https://github.com/Wizzard-TDS/EFT-Prueba-Final-Esteban-Silva.git

def buscar_codigo(codigo, juegos):
    codigo_limpio = codigo.strip().upper()
    for key in juegos:
        if key.upper() == codigo_limpio:
            return key
    return None


def obtener_titulo(elemento):
    return elemento[0]

def validar_codigo(codigo, juegos):
    if not codigo or codigo.strip() == "":
        return False
    if buscar_codigo(codigo, juegos) is not None: #Extraña manera que encontre de escribirlo pero funciona.
        return False
    return True

def validar_titulo(titulo):
    if titulo and len(titulo.strip()) > 0:
        return True
    return False

def validar_plataforma(plataforma):
    if plataforma and len(plataforma.strip()) > 0:
        return True
    return False

def validar_genero(genero):
    if genero and len(genero.strip()) > 0:
        return True
    return False

def validar_clasificacion(clasificacion):
    if clasificacion in ['E', 'T', 'M']:
        return True
    return False

def validar_multiplayer(multiplayer):
    if multiplayer.strip().lower() in ['s', 'n']:
        return True
    return False

def validar_editor(editor):
    if editor and len(editor.strip()) > 0:
        return True
    return False

def validar_precio(precio_str):
    try:
        val = int(precio_str)
        if val > 0:
            return True
        return False
    except ValueError:
        return False

def validar_stock(stock_str):
    try:
        val = int(stock_str)
        if val >= 0:
            return True
        return False
    except ValueError:
        return False

# =====================================

def stock_plataforma(plataforma, juegos, inventario):
    total_stock = 0
    plataforma_target = plataforma.strip().lower()
    
    for cod, datos_juego in juegos.items():
        if datos_juego[1].strip().lower() == plataforma_target:
            if cod in inventario:
                total_stock += inventario[cod][1]
                
    print(f"El total de stock disponibles es: {total_stock}")


def busqueda_precio(p_min, p_max, juegos, inventario):
    resultados = []
    
    for cod, datos_inv in inventario.items():
        precio = datos_inv[0]
        stock = datos_inv[1]
        
        if p_min <= precio <= p_max and stock > 0:
            if cod in juegos:
                titulo = juegos[cod][0]
                resultados.append((titulo, cod))
                
    if not resultados:
        print("No hay juegos en ese rango de precios.")
    else:
        resultados.sort(key=obtener_titulo)
        formateados = [f"{titulo}--{cod}" for titulo, cod in resultados]
        print(f"Los juegos encontrados son: {formateados}")


def actualizar_precio(codigo, nuevo_precio, juegos, inventario):
    cod_real = buscar_codigo(codigo, juegos)
    if cod_real is None or cod_real not in inventario:
        return False
    
    inventario[cod_real][0] = nuevo_precio
    return True


def agregar_juego(codigo, titulo, plataforma, genero, clasificacion, multiplayer, editor, precio, stock, juegos, inventario):
    if buscar_codigo(codigo, juegos) is not None:
        return False
    
    cod_clave = codigo.strip().upper()
    juegos[cod_clave] = [titulo.strip(), plataforma.strip(), genero.strip(), clasificacion, multiplayer, editor.strip()]
    inventario[cod_clave] = [precio, stock]
    return True


def eliminar_juego(codigo, juegos, inventario):
    cod_real = buscar_codigo(codigo, juegos)
    if cod_real is None:
        return False
    
    if cod_real in juegos:
        del juegos[cod_real]
    if cod_real in inventario:
        del inventario[cod_real]
    return True

# =====================

def mostrar_menu():
    print("======= MENÚ PRINCIPAL =======")
    print("1. Stock por plataforma")
    print("2. Búsqueda de juegos por rango de precio")
    print("3. Actualizar precio de juego")
    print("4. Agregar juego")
    print("5. Eliminar juego")
    print("6. Salir")

def leer_opcion():
    return input("Ingrese opción: ").strip()

def main():
    juegos = {
        'G001': ['Final Fantasy IX', 'PC', 'accion', 'T', False, 'Square Enix'],
        'G002': ['Rayman Retold', 'Switch', 'puzzle', 'E', True, 'Ubisoft'],
        'G003': ['Crazy taxi', 'PS5', 'aventura', 'T', True, 'OrionGames'],
        'G004': ['Forza Horizon 5', 'Xbox', 'carreras', 'E', True, 'VelocityLab'],
        'G005': ['Bravely Deafult', 'Switch', 'estrategia', 'E', False, 'Square Enix'],
        'G006': ['Skyrim', 'PC', 'estrategia', 'M', False, 'Bethesda']
    }

    inventario = {
        'G001': [9990, 7],
        'G002': [29990, 2],
        'G003': [12990, 3],
        'G004': [34990, 5],
        'G005': [20990, 9],
        'G006': [39990, 2]
    }


    while True:
        mostrar_menu()
        opcion = leer_opcion()

        if opcion == '1':
            plat = input("Ingrese plataforma a consultar: ")
            stock_plataforma(plat, juegos, inventario)


        elif opcion == '2':
            while True:
                p_min_str = input("Ingrese precio mínimo: ")
                p_max_str = input("Ingrese precio máximo: ")
                try:
                    p_min = int(p_min_str)
                    p_max = int(p_max_str)
                    if p_min >= 0 and p_max >= 0 and p_min < p_max:
                        busqueda_precio(p_min, p_max, juegos, inventario)
                        break
                    else:
                        print("Debe ingresar valores enteros")
                except ValueError:
                    print("Debe ingresar valores enteros")


        elif opcion == '3':
            repetir = 's'
            while repetir == 's':
                cod = input("Ingrese código del juego: ")
                nuevo_precio_str = input("Ingrese nuevo precio: ")
                
                if validar_precio(nuevo_precio_str):
                    nuevo_precio = int(nuevo_precio_str) #Espero que se entienda lo que quise hacer aquí
                    exito = actualizar_precio(cod, nuevo_precio, juegos, inventario)
                    if exito:
                        print("Precio actualizado")
                    else:
                        print("El código no existe")
                else:
                    print("El código no existe")

                repetir = input("¿Desea actualizar otro precio?: ").strip().lower()


        elif opcion == '4':
            cod = input("Ingrese código del juego: ")
            titulo = input("Ingrese título: ")
            plat = input("Ingrese plataforma: ")
            gen = input("Ingrese género: ")
            clas = input("Ingrese clasificación: ")
            mult_str = input("¿Es multiplayer? (s/n): ")
            edit = input("Ingrese editor: ")
            precio_str = input("Ingrese precio: ")
            stock_str = input("Ingrese stock: ")

            
            if not validar_codigo(cod, juegos):
                print("El código ya existe")
            elif not validar_titulo(titulo):
                print("El título no es válido")
            elif not validar_plataforma(plat):
                print("La plataforma no es válida")
            elif not validar_genero(gen):
                print("El género no es válido")
            elif not validar_clasificacion(clas):
                print("La clasificación no es válida")
            elif not validar_multiplayer(mult_str):
                print("El campo multiplayer no es válido")
            elif not validar_editor(edit):
                print("El editor no es válido")
            elif not validar_precio(precio_str):
                print("El precio no es válido")
            elif not validar_stock(stock_str):
                print("El stock no es válido")
            else:
                mult_bool = True if mult_str.strip().lower() == 's' else False
                precio_int = int(precio_str)
                stock_int = int(stock_str)

                agregado = agregar_juego(cod, titulo, plat, gen, clas, mult_bool, edit, precio_int, stock_int, juegos, inventario)
                if agregado:
                    print("Juego agregado")
                else:
                    print("El juego ya existe")


        elif opcion == '5':
            cod = input("Ingrese el código del juego a eliminar: ")
            eliminado = eliminar_juego(cod, juegos, inventario)
            if eliminado:
                print("Juego eliminado")
            else:
                print("El código no existe")

        elif opcion == '6':
            print("Programa finalizado.")
            break

        else:
            print("Debe seleccionar una opción válida")

main()

# salusai

from telegram import Update, ReplyKeyboardMarkup
from telegram import InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import CallbackQueryHandler
from telegram.ext import (
    ApplicationBuilder,
    CommandHandler,
    MessageHandler,
    ConversationHandler,
    filters,
    ContextTypes
)



salusai_db = {  # Tu base de datos aquí
    "categorias": {
        "1": {
            "nombre": "Síntomas",
            "subcategorias": {
                "1": {
                    "nombre": "Dolor de cabeza",
                    "preguntas": [
                        "¿Ha dormido bien últimamente?",
                        "¿Ha consumido suficiente agua?"
                    ],
                    "solucion": "Descansa en un lugar oscuro, bebe agua y evita el uso prolongado de pantallas. Si el dolor persiste, consulta a un médico."
                },
                "2": {
                    "nombre": "Fiebre",
                    "preguntas": [
                        "¿Su temperatura es superior a 38°C?",
                        "¿Tiene escalofríos?"
                    ],
                    "solucion": "Manténgase hidratado, repose y tome un antipirético si es necesario. Si la fiebre persiste más de 3 días, acuda al médico."
                },
                "3": {
                    "nombre": "Fatiga",
                    "preguntas": [
                        "¿Ha dormido menos de 6 horas por noche?",
                        "¿Se siente sin energía constantemente?"
                    ],
                    "solucion": "Asegúrese de dormir lo suficiente y mantener una dieta equilibrada. Consulte a un médico si la fatiga es persistente."
                },
                "4": {
                    "nombre": "Tos",
                    "preguntas": [
                        "¿La tos dura más de una semana?",
                        "¿Tiene dificultad para respirar?"
                    ],
                    "solucion": "Beba líquidos calientes y evite irritantes. Si la tos persiste o se acompaña de fiebre alta, consulte a un médico."
                }
            }
        },
        "2": {
            "nombre": "Bienestar",
            "subcategorias": {
                "1": {
                    "nombre": "Sueño",
                    "preguntas": [
                        "¿Duerme al menos 7 horas por noche?",
                        "¿Se despierta varias veces durante la noche?"
                    ],
                    "solucion": "Trate de establecer una rutina de sueño regular y evite pantallas antes de dormir."
                },
                "2": {
                    "nombre": "Ejercicio",
                    "preguntas": [
                        "¿Realiza actividad física al menos 3 veces por semana?",
                        "¿Siente dolor o rigidez después de hacer ejercicio?"
                    ],
                    "solucion": "Realice ejercicios de estiramiento y mantenga una rutina equilibrada."
                },
                "3": {
                    "nombre": "Manejo del estrés",
                    "preguntas": [
                        "¿Se siente frecuentemente abrumado?",
                        "¿Tiene dificultad para relajarse?"
                    ],
                    "solucion": "Pruebe técnicas de respiración y meditación para reducir el estrés."
                },
                "4": {
                    "nombre": "Hábitos saludables",
                    "preguntas": [
                        "¿Evita alimentos ultraprocesados?",
                        "¿Bebe suficiente agua diariamente?"
                    ],
                    "solucion": "Intente llevar una dieta balanceada y mantenerse hidratado."
                }
            }
        },
        "3": {
            "nombre": "Nutrición",
            "subcategorias": {
                "1": {
                    "nombre": "Deficiencias vitamínicas",
                    "preguntas": [
                        "¿Tiene uñas quebradizas o caída del cabello?",
                        "¿Se siente cansado sin razón aparente?"
                    ],
                    "solucion": "Consulte a un especialista y ajuste su dieta para incluir más vitaminas y minerales."
                },
                "2": {
                    "nombre": "Dietas específicas",
                    "preguntas": [
                        "¿Sigue una dieta restringida en ciertos alimentos?",
                        "¿Tiene dificultad para obtener ciertos nutrientes?"
                    ],
                    "solucion": "Asegúrese de obtener los nutrientes esenciales a través de suplementos o alternativas naturales."
                },
                "3": {
                    "nombre": "Hidratación",
                    "preguntas": [
                        "¿Bebe al menos 2 litros de agua al día?",
                        "¿Tiene la piel seca frecuentemente?"
                    ],
                    "solucion": "Aumente su ingesta de agua y consuma frutas con alto contenido de líquidos."
                },
                "4": {
                    "nombre": "Intolerancias alimentarias",
                    "preguntas": [
                        "¿Siente malestar después de consumir ciertos alimentos?",
                        "¿Ha sido diagnosticado con alguna alergia alimentaria?"
                    ],
                    "solucion": "Evite los alimentos que le causan malestar y consulte a un especialista para realizar pruebas de alergias."
                }
            }
        },
        "4": {
            "nombre": "Primeros auxilios",
            "subcategorias": {
                "1": {
                    "nombre": "Cortes",
                    "preguntas": [
                        "¿El corte es profundo?",
                        "¿Sangra abundantemente?"
                    ],
                    "solucion": "Lave la herida con agua y jabón, aplique presión y use un vendaje. Si es profundo, busque atención médica."
                },
                "2": {
                    "nombre": "Quemaduras",
                    "preguntas": [
                        "¿La quemadura es de segundo grado o más grave?",
                        "¿Siente mucho dolor en la zona afectada?"
                    ],
                    "solucion": "Enfríe la zona con agua durante 10 minutos y aplique una gasa estéril. Busque atención médica si es grave."
                },
                "3": {
                    "nombre": "Asfixia",
                    "preguntas": [
                        "¿La persona puede toser o hablar?",
                        "¿La persona ha perdido el conocimiento?"
                    ],
                    "solucion": "Si no puede respirar, realice la maniobra de Heimlich y busque ayuda médica."
                },
                "4": {
                    "nombre": "Fracturas",
                    "preguntas": [
                        "¿La extremidad afectada está deformada?",
                        "¿Siente un dolor intenso al moverla?"
                    ],
                    "solucion": "Inmovilice la zona afectada y busque atención médica de inmediato."
                }
            }
        },
        "5": {
            "nombre": "Salud mental",
            "subcategorias": {
                "1": {
                    "nombre": "Ansiedad",
                    "preguntas": [
                        "¿Ha sentido palpitaciones sin razón aparente?",
                        "¿Se siente constantemente preocupado?"
                    ],
                    "solucion": "Pruebe ejercicios de respiración y mindfulness para reducir la ansiedad."
                },
                "2": {
                    "nombre": "Depresión",
                    "preguntas": [
                        "¿Ha sentido tristeza constante por más de dos semanas?",
                        "¿Ha perdido interés en actividades que solía disfrutar?"
                    ],
                    "solucion": "Busque apoyo emocional y considere hablar con un profesional de la salud mental."
                },
                "3": {
                    "nombre": "Estrés",
                    "preguntas": [
                        "¿Siente tensión en el cuerpo con frecuencia?",
                        "¿Tiene dificultad para concentrarse?"
                    ],
                    "solucion": "Realice pausas activas y practique técnicas de relajación."
                },
                "4": {
                    "nombre": "Apoyo emocional",
                    "preguntas": [
                        "¿Siente que no tiene a quién acudir en momentos difíciles?",
                        "¿Le cuesta expresar sus emociones?"
                    ],
                    "solucion": "Intente hablar con alguien de confianza o busque ayuda profesional si lo necesita."
                }
            }
        }
    }
}


# Token del bot
TOKEN = "7805356757:AAExzNN-DFWYbEkF7iAQ9wSGip_oTck7iuw"  # Reemplaza con tu token real

# Estados de la conversación
( OBTENER_NOMBRE,
 OBTENER_APELLIDOS,
 CONFIRMAR_DATOS,
 SELECCIONAR_CATEGORIA,
 SELECCIONAR_SUBCATEGORIA,
 RESPONDER_PREGUNTAS,
 MOSTRAR_SOLUCION,
 OFRECER_MAS_AYUDA,
 PREGUNTAR_SATISFACCION,
 RECIBIR_SATISFACCION
   ) = range(10)

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Inicia la conversación y pide el nombre."""
    await update.message.reply_text(" ✨ ¡Hola! Bienvenido al Chatbot de SALUSAI! 🤖💙. \nSoy tu asistente virtual  y estoy aquí para ayudarte.\n\n💬 Dime tu nombre, por favor: 😊")
    return OBTENER_NOMBRE

async def obtener_nombre(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Guarda el nombre y pide el apellido."""
    context.user_data["name"] = update.message.text
    await update.message.reply_text("🙌 ¡Gracias! \nAhora dime tu apellido, por favor:")
    return OBTENER_APELLIDOS

async def obtener_apellidos(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Guarda el apellido y confirma los datos."""
    context.user_data["last_name"] = update.message.text
    await update.message.reply_text(
        f"✅ ¡GRACIAS! Tus datos son:\nNombre: {context.user_data.get('name', 'No especificado')}\nApellido: {context.user_data.get('last_name', 'No especificado')}\n\n¿Es correcto? Responde ✅ SI / ❌ NO"
    )
    return CONFIRMAR_DATOS




async def confirmar_datos(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Confirma los datos y muestra las categorías."""
    respuesta = update.message.text.strip().lower()
    if respuesta == "si":
        # Crear la lista de categorías con números
        categorias_texto = "\n".join([f"{cat_id}. {cat_data['nombre']}" for cat_id, cat_data in salusai_db["categorias"].items()])

        await update.message.reply_text(
            " ✨¡Perfecto! ✨ \nContinuemos con el proceso.\n\n📌¿En qué categoría deseas obtener información?\n\n"
            f"{categorias_texto}\n\n"
            "🔹 Seleccione una opción (1-5):"
        )
        return SELECCIONAR_CATEGORIA  # Ir al siguiente estado

    elif respuesta == "no":
        await update.message.reply_text("🔄Entiendo, volvamos al menú inicial. \n😊 Dime tu nombre, por favor:")
        return OBTENER_NOMBRE
    else:
        await update.message.reply_text("🌟 ¡Genial! Solo quiero asegurarme de que todo esté bien.\n\n❓ Por favor, responde con 👉 SI o NO.\n¿Es correcto? 😊")
        return CONFIRMAR_DATOS

async def seleccionar_categoria(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Guarda la categoría seleccionada y muestra las subcategorías."""
    try:
        categoria_id = int(update.message.text)  # Obtener el número de categoría
        if 1 <= categoria_id <= len(salusai_db["categorias"]):
            categoria_nombre = salusai_db["categorias"][str(categoria_id)]["nombre"]
            context.user_data["categoria"] = categoria_nombre

            # Obtener las subcategorías para la categoría seleccionada

            subcategorias_nombres = [
            f"{sub_id}. {sub_data['nombre']}"  # Incluir números de subcategoría
                for sub_id, sub_data in salusai_db["categorias"][str(categoria_id)]["subcategorias"].items()]

            await update.message.reply_text(f"✅ Perfecto, has seleccionado la categoría: {categoria_nombre}🎯.\n 📌 Ahora, elige una subcategoría para seguir explorando juntos: 😊")
            await update.message.reply_text("📌 Ahora, elige una subcategoría:\n Seleccione una opción (1-4) ⬇️😊 ")


            # Mostrar las subcategorías con números
            await update.message.reply_text("\n".join(subcategorias_nombres))

            return SELECCIONAR_SUBCATEGORIA  # Ir al siguiente estado
        else:
            await update.message.reply_text("⚠️ Ups! Esa opción no es válida.\n Por favor, selecciona un número del 1 al 5 🔢😊.")
            return SELECCIONAR_CATEGORIA  # Volver a pedir la categoría
    except ValueError:
        await update.message.reply_text("⚠️ Oops! La entrada no es válida.\n Por favor, ingresa un número válido. 🔢😊")
        return SELECCIONAR_CATEGORIA  # Volver a pedir la categoría




async def seleccionar_subcategoria(update: Update, context: ContextTypes.DEFAULT_TYPE):
    
    try:
        subcategoria_id = update.message.text.strip()
        categoria_nombre = context.user_data.get("categoria")

        if categoria_nombre:
            categoria_data = next(
                (data for key, data in salusai_db["categorias"].items() if data["nombre"] == categoria_nombre), None
            )

            if categoria_data:
                subcategorias = categoria_data["subcategorias"]

                if subcategoria_id in subcategorias:
                    subcategoria_data = subcategorias[subcategoria_id]
                    subcategoria_nombre = subcategoria_data["nombre"]
                    context.user_data["subcategoria"] = subcategoria_nombre

                    await update.message.reply_text(f"✅Has seleccionado la subcategoría: {subcategoria_nombre} 🎯.")
                    await update.message.reply_text(f"✨ ¡Muy bien! ✨\n Ahora, por favor, responde a las siguientes preguntas con\n Sí o No. 😊✔❌ ")

                    preguntas = subcategoria_data.get("preguntas", [])
                    if preguntas:
                        context.user_data["preguntas"] = preguntas
                        context.user_data["respuestas"] = {}
                        pregunta = preguntas.pop(0)
                        context.user_data["pregunta_actual"] = pregunta
                        await update.message.reply_text(pregunta)
                        return RESPONDER_PREGUNTAS
                    else:
                        await update.message.reply_text("🤔 Parece que esta subcategoría no tiene preguntas disponibles. \n¿Te gustaría elegir otra opción? 😊.")
                        return SELECCIONAR_CATEGORIA  # O devolver al menú anterior
                else:
                    await update.message.reply_text("⚠️ Opción inválida ⚠️.\n Por favor, selecciona un número válido. 😊")
                    return SELECCIONAR_SUBCATEGORIA
            else:
                await update.message.reply_text("❌ Error ❌ : No se encontró la categoría seleccionada. \nPor favor, intenta nuevamente. 😊")
                return SELECCIONAR_CATEGORIA
        else:
            await update.message.reply_text("📌 Primero debes seleccionar una categoría para continuar. 😊")
            return SELECCIONAR_CATEGORIA

    except Exception as e:
        print(f"Error en seleccionar_subcategoria: {e}")
        await update.message.reply_text("Ups! Algo salió mal.\nPor favor, inténtalo de nuevo. 🙁")
        return SELECCIONAR_CATEGORIA



async def responder_preguntas(update: Update, context: ContextTypes.DEFAULT_TYPE):
    # Si ya no hay pregunta activa, pasa al siguiente estado
    if context.user_data.get("pregunta_actual") is None:
        return await mostrar_solucion(update, context)

    respuesta = update.message.text.strip().lower()
    pregunta_actual = context.user_data.get("pregunta_actual")
    if respuesta in ["sí", "si", "no"]:
        context.user_data["respuestas"][pregunta_actual] = respuesta
        preguntas = context.user_data.get("preguntas", [])
        if preguntas:
            pregunta = preguntas.pop(0)
            context.user_data["pregunta_actual"] = pregunta
            context.user_data["preguntas"] = preguntas
            await update.message.reply_text(pregunta)
            return RESPONDER_PREGUNTAS
        else:
            context.user_data.pop("pregunta_actual", None)
            context.user_data.pop("preguntas", None)
            return await mostrar_solucion(update, context)
    else:
        await update.message.reply_text("⚠️ Responde solo con Sí o No.")
        return RESPONDER_PREGUNTAS





async def mostrar_solucion(update: Update, context: ContextTypes.DEFAULT_TYPE): 
    """Muestra la solución basada en las respuestas dadas y luego ofrece más ayuda."""
    


    categoria = context.user_data.get("categoria")
    subcategoria = context.user_data.get("subcategoria")

    if categoria and subcategoria:
        # Busca la categoría seleccionada en la base de datos
        categoria_data = next(
            (data for key, data in salusai_db["categorias"].items() if data["nombre"] == categoria),
            None
        )
        if categoria_data:
            # Busca la subcategoría seleccionada dentro de la categoría
            subcategoria_data = next(
                (data for key, data in categoria_data["subcategorias"].items() if data["nombre"] == subcategoria),
                None
            )
            if subcategoria_data:
                # Obtiene la solución definida para esa subcategoría
                solucion = subcategoria_data.get("solucion", "No se encontró una solución específica.")

                mensaje = (
                    "¡Muchas gracias por tu respuesta✨\n"
                    "Aquí tienes algunas recomendaciones pensadas especialmente para ti: \n\n"
                    f"💡 {solucion}"
                )

                # Envía el mensaje de solución al usuario
                await update.message.reply_text(mensaje)

                # Luego, se ofrece más ayuda con un nuevo mensaje
                await update.message.reply_text(
                    "❓ ¿Necesitas más ayuda?\n"
                    "Responde con un número:\n"
                    "1️⃣ - Sí, quiero ver las categorías nuevamente.\n"
                    "2️⃣ - No, quiero finalizar con una encuesta de satisfacción."
                )
            
                return OFRECER_MAS_AYUDA


async def ofrecer_mas_ayuda(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """
    Maneja la respuesta del usuario sobre si necesita más ayuda.
    Según el número que responda, se muestra la lista de categorías o se procede a la encuesta de satisfacción.
    """
    respuesta = update.message.text.strip()

    if respuesta == "1":
        # Limpia datos residuales si los hubiera.
        context.user_data.pop("pregunta_actual", None)
        context.user_data.pop("preguntas", None)
        
        # Muestra nuevamente las categorías.
        categorias_texto = "\n".join(
            [f"{cat_id}. {cat_data['nombre']}" for cat_id, cat_data in salusai_db["categorias"].items()]
        )
        await update.message.reply_text(
            "✨ ¡Genial! Estoy aquí para ayudarte. 😊\n\n"
            "📌 ¿En qué categoría deseas obtener información?\n\n"
            f"{categorias_texto}\n\n"
            "🔹 Seleccione una opción (1-5):"
        )
        return SELECCIONAR_CATEGORIA

    elif respuesta == "2":
        return await preguntar_satisfaccion(update, context)

    else:
        await update.message.reply_text("⚠️ Por favor, responde con 1 o 2.")
        return OFRECER_MAS_AYUDA



async def preguntar_satisfaccion(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Pregunta la satisfacción del usuario después de la consulta."""
    await update.message.reply_text(
        "🙏 Nos gustaría saber tu opinión. ¿Qué tan satisfecho estás con la atención recibida? 😊\n\n"
        "✨ Por favor, responde con un número del 1 al 5 ✨\n\n"
        "1️⃣ - Muy insatisfecho 😞\n"
        "2️⃣ - Insatisfecho 😕\n"
        "3️⃣ - Neutral 😐\n"
        "4️⃣ - Satisfecho 🙂\n"
        "5️⃣ - Muy satisfecho 😃"
    )
    return RECIBIR_SATISFACCION



async def recibir_satisfaccion(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Recibe la respuesta de satisfacción y envía una respuesta acorde."""
    respuestas = {
        "1": "😞 Lo siento si no fue útil. Intentaré mejorar.",
        "2": "🙁 Gracias por tu feedback, seguiré mejorando.",
        "3": "😐 Gracias, ¿en qué podría mejorar?",
        "4": "🙂 ¡Me alegra que haya sido útil!",
        "5": "😃 ¡Gracias por tu excelente calificación!",
    }

    calificacion = update.message.text.strip()
    
    if calificacion in respuestas:
        await update.message.reply_text(respuestas[calificacion])
        await update.message.reply_text("😊 ¡Gracias por usar SALUSAI! Que tengas un excelente día. 🌟")
        return ConversationHandler.END
    else:
        await update.message.reply_text("⚠️ Por favor, responde con un número del 1 al 5.")
        return PREGUNTAR_SATISFACCION








async def cancel(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Cancela la conversación."""
    await update.message.reply_text("Operación cancelada.")
    return ConversationHandler.END

# Construcción de la aplicación
application = ApplicationBuilder().token(TOKEN).build()

# Manejo de la conversación
conv_handler = ConversationHandler(
    entry_points=[CommandHandler("start", start)],
    states={
        OBTENER_NOMBRE: [MessageHandler(filters.TEXT & ~filters.COMMAND, obtener_nombre)],
        OBTENER_APELLIDOS: [MessageHandler(filters.TEXT & ~filters.COMMAND, obtener_apellidos)],
        CONFIRMAR_DATOS: [MessageHandler(filters.TEXT & ~filters.COMMAND, confirmar_datos)],
        SELECCIONAR_CATEGORIA: [MessageHandler(filters.TEXT & ~filters.COMMAND, seleccionar_categoria)],
        SELECCIONAR_SUBCATEGORIA: [MessageHandler(filters.TEXT & ~filters.COMMAND, seleccionar_subcategoria)], 
        RESPONDER_PREGUNTAS: [MessageHandler(filters.TEXT & ~filters.COMMAND, responder_preguntas)], 
        MOSTRAR_SOLUCION: [MessageHandler(filters.TEXT & ~filters.COMMAND, mostrar_solucion)], 
        OFRECER_MAS_AYUDA: [MessageHandler(filters.TEXT & ~filters.COMMAND, ofrecer_mas_ayuda)], 
        PREGUNTAR_SATISFACCION: [MessageHandler(filters.TEXT & ~filters.COMMAND, preguntar_satisfaccion)], 
        RECIBIR_SATISFACCION: [MessageHandler(filters.TEXT & ~filters.COMMAND, recibir_satisfaccion)], 

     },
    fallbacks=[CommandHandler("cancel", cancel)],
)

# Agregar handlers al bot
application.add_handler(conv_handler)

# Ejecutar el bot
application.run_polling()

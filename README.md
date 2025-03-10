# ejercicio2
    import pandas as pd
    import os

    def analyze_demographic_data():
        # Verificar si el archivo existe
        file_path = "adult.data.csv"
    if not os.path.exists(file_path):
        raise FileNotFoundError(f"El archivo {file_path} no se encuentra en el directorio actual.")
    
    # Cargar los datos con nombres de columnas
    column_names = ["age", "workclass", "fnlwgt", "education", "education-num", "marital-status", "occupation", "relationship", "race", "sex", "capital-gain", "capital-loss", "hours-per-week", "native-country", "salary"]
    df = pd.read_csv(file_path, names=column_names, skipinitialspace=True, header=None)
    
    # Limpiar datos: eliminar espacios en texto
    df = df.applymap(lambda x: x.strip() if isinstance(x, str) else x)
    
    # Convertir columnas numéricas
    for col in ["age", "hours-per-week", "capital-gain", "capital-loss"]:
        df[col] = pd.to_numeric(df[col], errors="coerce")
    
    # Eliminar filas con valores nulos
    df = df.dropna()
    
    # 1. Número de personas por raza
    race_count = df["race"].value_counts()
    
    # 2. Edad media de los hombres
    average_age_men = round(df[df["sex"] == "Male"]["age"].mean(), 1)
    
    # 3. Porcentaje de personas con licenciatura
    percentage_bachelors = round((df["education"].value_counts(normalize=True).get("Bachelors", 0)) * 100, 1)
    
    # 4. Porcentaje de personas con educación avanzada que ganan >50K
    advanced_education = df["education"].isin(["Bachelors", "Masters", "Doctorate"])
    high_edu_total = df[advanced_education].shape[0]
    high_edu_rich = df[advanced_education & (df["salary"] == ">50K")].shape[0]
    higher_education_rich = round((high_edu_rich / high_edu_total) * 100, 1) if high_edu_total > 0 else 0
    
    # 5. Porcentaje de personas sin educación avanzada que ganan >50K
    lower_education = ~advanced_education
    low_edu_total = df[lower_education].shape[0]
    low_edu_rich = df[lower_education & (df["salary"] == ">50K")].shape[0]
    lower_education_rich = round((low_edu_rich / low_edu_total) * 100, 1) if low_edu_total > 0 else 0
    
    # 6. Número mínimo de horas trabajadas por semana
    min_work_hours = df["hours-per-week"].min()
    
    # 7. Porcentaje de personas que trabajan el mínimo de horas semanales y ganan >50K
    min_workers = df[df["hours-per-week"] == min_work_hours]
    rich_min_workers = min_workers[min_workers["salary"] == ">50K"].shape[0]
    rich_percentage = round((rich_min_workers / min_workers.shape[0]) * 100, 1) if min_workers.shape[0] > 0 else 0
    
    # 8. País con el mayor porcentaje de personas que ganan >50K
    country_salary = df[df["salary"] == ">50K"]["native-country"].value_counts() / df["native-country"].value_counts()
    country_salary = country_salary.dropna()
    highest_earning_country = country_salary.idxmax()
    highest_earning_country_percentage = round(country_salary.max() * 100, 1)
    
    # 9. Ocupación más común en India para personas con >50K
    india_high_earners = df[(df["native-country"] == "India") & (df["salary"] == ">50K")]
    top_IN_occupation = india_high_earners["occupation"].value_counts().idxmax() if not india_high_earners.empty else "Ninguna ocupación encontrada"
    
    # Imprimir los resultados claramente
    print("\n--- Resultados del Análisis Demográfico ---\n")
    print(f"1️⃣ Número de personas por raza:\n{race_count}\n")
    print(f"2️⃣ Edad media de los hombres: {average_age_men} años\n")
    print(f"3️⃣ Porcentaje de personas con licenciatura: {percentage_bachelors}%\n")
    print(f"4️⃣ Porcentaje de personas con educación avanzada que ganan >50K: {higher_education_rich}%\n")
    print(f"5️⃣ Porcentaje de personas sin educación avanzada que ganan >50K: {lower_education_rich}%\n")
    print(f"6️⃣ Número mínimo de horas trabajadas por semana: {min_work_hours} horas\n")
    print(f"7️⃣ Porcentaje de personas que trabajan el mínimo de horas semanales y ganan >50K: {rich_percentage}%\n")
    print(f"8️⃣ País con el mayor porcentaje de personas que ganan >50K: {highest_earning_country} ({highest_earning_country_percentage}%)\n")
    print(f"9️⃣ Ocupación más común en India para quienes ganan >50K: {top_IN_occupation}\n")

    if __name__ == "__main__":
        try:
            analyze_demographic_data()
        except FileNotFoundError as e:
            print(e)
        except Exception as e:
            print(f"Error inesperado: {e}")

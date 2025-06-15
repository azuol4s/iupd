"use client"

import type React from "react"

import { useState } from "react"
import { Button } from "@/components/ui/button"
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card"
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"
import { RadioGroup, RadioGroupItem } from "@/components/ui/radio-group"
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs"
import { Alert, AlertDescription, AlertTitle } from "@/components/ui/alert"
import { InfoIcon } from "lucide-react"

type BMICategory = "mazas" | "normalus" | "antsvoris" | "nutukimas"

interface UserData {
  weight: number
  height: number
  age: number
  gender: "vyras" | "moteris"
}

export default function SveikatosVertinimoSistema() {
  const [userData, setUserData] = useState<UserData>({
    weight: 0,
    height: 0,
    age: 0,
    gender: "vyras",
  })
  const [bmi, setBmi] = useState<number | null>(null)
  const [bmiCategory, setBmiCategory] = useState<BMICategory | null>(null)

  const handleInputChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target
    setUserData((prev) => ({
      ...prev,
      [name]: name === "gender" ? value : Number(value),
    }))
  }

  const handleGenderChange = (value: "vyras" | "moteris") => {
    setUserData((prev) => ({
      ...prev,
      gender: value,
    }))
  }

  const calculateBMI = () => {
    if (userData.weight <= 0 || userData.height <= 0 || userData.age <= 0) {
      alert("Prašome įvesti teisingus duomenis")
      return
    }

    const heightInMeters = userData.height / 100
    const calculatedBMI = userData.weight / (heightInMeters * heightInMeters)
    setBmi(Number.parseFloat(calculatedBMI.toFixed(2)))

    // Nustatyti BMI kategoriją
    if (calculatedBMI < 18.5) {
      setBmiCategory("mazas")
    } else if (calculatedBMI >= 18.5 && calculatedBMI < 24.9) {
      setBmiCategory("normalus")
    } else if (calculatedBMI >= 25 && calculatedBMI < 29.9) {
      setBmiCategory("antsvoris")
    } else {
      setBmiCategory("nutukimas")
    }
  }

  const getRecommendations = () => {
    if (!bmiCategory) return null

    const recommendations = {
      mazas: {
        title: "Mažas svoris (VMI < 18.5)",
        description: (
          <>
            <p className="mb-2">
              <strong>Tikslas:</strong> priaugti svorio sveikai.
            </p>
            <p className="mb-2">
              <strong>Kasdienis kalorijų perteklius:</strong> +300–500 kcal.
            </p>
            <p className="mb-2">
              <strong>Maisto produktai:</strong> riešutai, avokadai, riebi žuvis, ryžiai, avižos, riebi varškė,
              alyvuogių aliejus.
            </p>
            <p className="mb-2">
              <strong>Sportas:</strong> 3–4 kartus per savaitę jėgos treniruotės, minimalus kardio.
            </p>
            <p className="mb-2">
              <strong>Miegas:</strong> 8–9 valandos per parą.
            </p>
          </>
        ),
      },
      normalus: {
        title: "Normalus svoris (18.5 ≤ VMI < 24.9)",
        description: (
          <>
            <p className="mb-2">
              <strong>Tikslas:</strong> išlaikyti sveikatą.
            </p>
            <p className="mb-2">
              <strong>Subalansuota mityba:</strong> daug daržovių, vaisių, liesų baltymų, kompleksinių angliavandenių.
            </p>
            <p className="mb-2">
              <strong>Sportas:</strong> 3–5 kartus per savaitę jėgos + kardio treniruotės.
            </p>
            <p className="mb-2">
              <strong>Kasdienis aktyvumas:</strong> 10 000 žingsnių, pertraukos kas 1 val. sėdint.
            </p>
          </>
        ),
      },
      antsvoris: {
        title: "Antsvoris (25 ≤ VMI < 29.9)",
        description: (
          <>
            <p className="mb-2">
              <strong>Tikslas:</strong> palaipsniui mažinti svorį.
            </p>
            <p className="mb-2">
              <strong>Kalorijų deficitas:</strong> -300–500 kcal.
            </p>
            <p className="mb-2">
              <strong>Mityba:</strong> mažinti cukrų, perdirbtą maistą, didinti daržovių ir baltymų suvartojimą.
            </p>
            <p className="mb-2">
              <strong>Sportas:</strong> bent 150 min vidutinio intensyvumo kardio + 2–3 jėgos treniruotės per savaitę.
            </p>
            <p className="mb-2">
              <strong>Tikslas:</strong> -0,5 kg per savaitę.
            </p>
          </>
        ),
      },
      nutukimas: {
        title: "Nutukimas (VMI ≥ 30)",
        description: (
          <>
            <p className="mb-2">
              <strong>Tikslas:</strong> reikšmingai pagerinti sveikatos būklę.
            </p>
            <p className="mb-2">
              <strong>Kalorijų deficitas:</strong> -500–700 kcal (atsargiai).
            </p>
            <p className="mb-2">
              <strong>Mityba:</strong> kontroliuotas valgymas, dienoraščio vedimas, didelis daržovių kiekis.
            </p>
            <p className="mb-2">
              <strong>Sportas:</strong> pradėti nuo pasivaikščiojimų, palaipsniui didinti apkrovą.
            </p>
            <p className="mb-2">
              <strong>Rekomenduojama gydytojo konsultacija.</strong>
            </p>
          </>
        ),
      },
    }

    return recommendations[bmiCategory]
  }

  const getMealPlan = () => {
    if (!bmiCategory) return null

    const mealPlans = {
      mazas: {
        title: "Dienos mitybos planas (svorio priaugimui)",
        meals: [
          {
            title: "Pusryčiai",
            description: "Avižinė košė su riešutų sviestu, bananais ir medumi (500 kcal)",
          },
          {
            title: "Užkandis",
            description: "Graikiškas jogurtas su riešutais ir uogomis (300 kcal)",
          },
          {
            title: "Pietūs",
            description: "Vištienos krūtinėlė su ryžiais ir daržovėmis, alyvuogių aliejus (650 kcal)",
          },
          {
            title: "Užkandis",
            description: "Avokadas su skrebučiais ir kiaušiniu (400 kcal)",
          },
          {
            title: "Vakarienė",
            description: "Lašiša su bulvių koše ir šparaginėmis pupelėmis (550 kcal)",
          },
        ],
      },
      normalus: {
        title: "Dienos mitybos planas (svorio palaikymui)",
        meals: [
          {
            title: "Pusryčiai",
            description: "Kiaušinienė su daržovėmis ir pilno grūdo duona (400 kcal)",
          },
          {
            title: "Užkandis",
            description: "Obuolys su migdolų riešutais (200 kcal)",
          },
          {
            title: "Pietūs",
            description: "Tuno salotos su avinžirniais ir daržovėmis (450 kcal)",
          },
          {
            title: "Užkandis",
            description: "Morkos su humusu (150 kcal)",
          },
          {
            title: "Vakarienė",
            description: "Kalakutienos maltinukai su saldžiomis bulvėmis ir brokoliais (400 kcal)",
          },
        ],
      },
      antsvoris: {
        title: "Dienos mitybos planas (svorio mažinimui)",
        meals: [
          {
            title: "Pusryčiai",
            description: "Kiaušinių omletas su špinatais ir pomidorais (300 kcal)",
          },
          {
            title: "Užkandis",
            description: "Mažas obuolys (80 kcal)",
          },
          {
            title: "Pietūs",
            description: "Vištienos salotos su daržovėmis ir citrinos sulčių užpilu (350 kcal)",
          },
          {
            title: "Užkandis",
            description: "Morkų lazdelės su humusu (100 kcal)",
          },
          {
            title: "Vakarienė",
            description: "Kepta žuvis su garuose virtomis daržovėmis (300 kcal)",
          },
        ],
      },
      nutukimas: {
        title: "Dienos mitybos planas (reikšmingam svorio mažinimui)",
        meals: [
          {
            title: "Pusryčiai",
            description: "Graikiškas jogurtas su uogomis (200 kcal)",
          },
          {
            title: "Užkandis",
            description: "Agurko ir morkų lazdelės (50 kcal)",
          },
          {
            title: "Pietūs",
            description: "Daržovių sriuba su vištienos krūtinėle (250 kcal)",
          },
          {
            title: "Užkandis",
            description: "Nedidelis vaisius (70 kcal)",
          },
          {
            title: "Vakarienė",
            description: "Troškinta liesa mėsa su daržovėmis (300 kcal)",
          },
        ],
      },
    }

    return mealPlans[bmiCategory]
  }

  const getWorkoutPlan = () => {
    if (!bmiCategory) return null

    const workoutPlans = {
      mazas: {
        title: "Savaitės treniruočių planas (svorio priaugimui)",
        days: [
          {
            day: "Pirmadienis",
            workout: "Viso kūno jėgos treniruotė (pagrindiniai pratimai: pritūpimai, atsispaudimai, prisitraukimai)",
          },
          {
            day: "Antradienis",
            workout: "Poilsio diena (lengvas pasivaikščiojimas)",
          },
          {
            day: "Trečiadienis",
            workout: "Viršutinės kūno dalies jėgos treniruotė (krūtinė, pečiai, rankos)",
          },
          {
            day: "Ketvirtadienis",
            workout: "Poilsio diena (tempimo pratimai)",
          },
          {
            day: "Penktadienis",
            workout: "Apatinės kūno dalies jėgos treniruotė (kojos, sėdmenys)",
          },
          {
            day: "Šeštadienis",
            workout: "Lengva kardio treniruotė (20-30 min)",
          },
          {
            day: "Sekmadienis",
            workout: "Visiško poilsio diena",
          },
        ],
      },
      normalus: {
        title: "Savaitės treniruočių planas (svorio palaikymui)",
        days: [
          {
            day: "Pirmadienis",
            workout: "Kardio treniruotė (30-40 min bėgimas, plaukimas arba dviratis)",
          },
          {
            day: "Antradienis",
            workout: "Viršutinės kūno dalies jėgos treniruotė",
          },
          {
            day: "Trečiadienis",
            workout: "Vidutinio intensyvumo intervalinė treniruotė (HIIT)",
          },
          {
            day: "Ketvirtadienis",
            workout: "Apatinės kūno dalies jėgos treniruotė",
          },
          {
            day: "Penktadienis",
            workout: "Kardio treniruotė (30-40 min)",
          },
          {
            day: "Šeštadienis",
            workout: "Viso kūno jėgos treniruotė",
          },
          {
            day: "Sekmadienis",
            workout: "Aktyvus poilsis (pasivaikščiojimas, joga)",
          },
        ],
      },
      antsvoris: {
        title: "Savaitės treniruočių planas (svorio mažinimui)",
        days: [
          {
            day: "Pirmadienis",
            workout: "Kardio treniruotė (40-50 min vidutinio intensyvumo)",
          },
          {
            day: "Antradienis",
            workout: "Viso kūno jėgos treniruotė (daugiau pakartojimų, mažesni svoriai)",
          },
          {
            day: "Trečiadienis",
            workout: "Intervalinė treniruotė (HIIT) - 25 min",
          },
          {
            day: "Ketvirtadienis",
            workout: "Aktyvus poilsis (10,000 žingsnių)",
          },
          {
            day: "Penktadienis",
            workout: "Kardio + jėgos treniruotė (apjungta)",
          },
          {
            day: "Šeštadienis",
            workout: "Ilgesnis kardio (60 min ėjimas, plaukimas arba dviratis)",
          },
          {
            day: "Sekmadienis",
            workout: "Tempimo pratimai, joga",
          },
        ],
      },
      nutukimas: {
        title: "Savaitės treniruočių planas (reikšmingam svorio mažinimui)",
        days: [
          {
            day: "Pirmadienis",
            workout: "Pasivaikščiojimas (20-30 min)",
          },
          {
            day: "Antradienis",
            workout: "Lengvi jėgos pratimai su kūno svoriu (10-15 min)",
          },
          {
            day: "Trečiadienis",
            workout: "Pasivaikščiojimas (20-30 min)",
          },
          {
            day: "Ketvirtadienis",
            workout: "Poilsio diena (tempimo pratimai)",
          },
          {
            day: "Penktadienis",
            workout: "Lengvi jėgos pratimai su kūno svoriu (10-15 min)",
          },
          {
            day: "Šeštadienis",
            workout: "Pasivaikščiojimas (30-40 min)",
          },
          {
            day: "Sekmadienis",
            workout: "Poilsio diena",
          },
        ],
      },
    }

    return workoutPlans[bmiCategory]
  }

  const getWaterRecommendation = () => {
    if (!userData.weight || userData.weight <= 0) return null

    const minWater = ((userData.weight * 30) / 1000).toFixed(1)
    const maxWater = ((userData.weight * 35) / 1000).toFixed(1)

    return {
      title: "Vandens vartojimo rekomendacija",
      description: `Kasdien rekomenduojama išgerti ${minWater}–${maxWater} l vandens (30–35 ml už kiekvieną kūno kilogramą).`,
    }
  }

  const getMotivationalMessages = () => {
    return [
      "Kiekviena maža pergalė yra žingsnis į tikslą!",
      "Nebūk griežtas sau – būk pastovus.",
      "Tu stipresnis nei manai – tęsk!",
    ]
  }

  return (
    <div className="container mx-auto py-8 px-4">
      <h1 className="text-3xl font-bold text-center mb-8">Sveikatos Vertinimo Sistema</h1>

      <div className="grid grid-cols-1 md:grid-cols-2 gap-8 mb-8">
        <Card>
          <CardHeader>
            <CardTitle>Įveskite savo duomenis</CardTitle>
            <CardDescription>Užpildykite visus laukelius, kad gautumėte tikslias rekomendacijas</CardDescription>
          </CardHeader>
          <CardContent>
            <div className="space-y-4">
              <div className="space-y-2">
                <Label htmlFor="weight">Kūno svoris (kg)</Label>
                <Input
                  id="weight"
                  name="weight"
                  type="number"
                  placeholder="Pvz., 70"
                  value={userData.weight || ""}
                  onChange={handleInputChange}
                />
              </div>

              <div className="space-y-2">
                <Label htmlFor="height">Ūgis (cm)</Label>
                <Input
                  id="height"
                  name="height"
                  type="number"
                  placeholder="Pvz., 175"
                  value={userData.height || ""}
                  onChange={handleInputChange}
                />
              </div>

              <div className="space-y-2">
                <Label htmlFor="age">Amžius (metais)</Label>
                <Input
                  id="age"
                  name="age"
                  type="number"
                  placeholder="Pvz., 30"
                  value={userData.age || ""}
                  onChange={handleInputChange}
                />
              </div>

              <div className="space-y-2">
                <Label>Lytis</Label>
                <RadioGroup
                  defaultValue="vyras"
                  value={userData.gender}
                  onValueChange={(value) => handleGenderChange(value as "vyras" | "moteris")}
                >
                  <div className="flex items-center space-x-2">
                    <RadioGroupItem value="vyras" id="vyras" />
                    <Label htmlFor="vyras">Vyras</Label>
                  </div>
                  <div className="flex items-center space-x-2">
                    <RadioGroupItem value="moteris" id="moteris" />
                    <Label htmlFor="moteris">Moteris</Label>
                  </div>
                </RadioGroup>
              </div>

              <Button className="w-full" onClick={calculateBMI}>
                Apskaičiuoti VMI
              </Button>
            </div>
          </CardContent>
        </Card>

        {bmi !== null && (
          <Card>
            <CardHeader>
              <CardTitle>Jūsų rezultatai</CardTitle>
              <CardDescription>Kūno masės indeksas (VMI) ir kategorija</CardDescription>
            </CardHeader>
            <CardContent>
              <div className="space-y-4">
                <div className="p-4 bg-muted rounded-lg text-center">
                  <h3 className="text-xl font-semibold mb-2">Jūsų VMI</h3>
                  <p className="text-4xl font-bold">{bmi}</p>
                </div>

                {bmiCategory && (
                  <Alert>
                    <InfoIcon className="h-4 w-4" />
                    <AlertTitle>Kategorija</AlertTitle>
                    <AlertDescription>
                      {bmiCategory === "mazas" && "Mažas svoris (VMI < 18.5)"}
                      {bmiCategory === "normalus" && "Normalus svoris (18.5 ≤ VMI < 24.9)"}
                      {bmiCategory === "antsvoris" && "Antsvoris (25 ≤ VMI < 29.9)"}
                      {bmiCategory === "nutukimas" && "Nutukimas (VMI ≥ 30)"}
                    </AlertDescription>
                  </Alert>
                )}

                {getWaterRecommendation() && (
                  <div className="p-4 bg-blue-50 rounded-lg">
                    <h3 className="font-semibold mb-1">{getWaterRecommendation()?.title}</h3>
                    <p>{getWaterRecommendation()?.description}</p>
                  </div>
                )}

                <div className="p-4 bg-green-50 rounded-lg">
                  <h3 className="font-semibold mb-2">Motyvacija</h3>
                  <ul className="list-disc pl-5 space-y-1">
                    {getMotivationalMessages().map((message, index) => (
                      <li key={index}>{message}</li>
                    ))}
                  </ul>
                </div>
              </div>
            </CardContent>
          </Card>
        )}
      </div>

      {bmiCategory && (
        <Tabs defaultValue="recommendations" className="w-full">
          <TabsList className="grid grid-cols-3 mb-8">
            <TabsTrigger value="recommendations">Rekomendacijos</TabsTrigger>
            <TabsTrigger value="meal-plan">Mitybos planas</TabsTrigger>
            <TabsTrigger value="workout-plan">Treniruočių planas</TabsTrigger>
          </TabsList>

          <TabsContent value="recommendations">
            <Card>
              <CardHeader>
                <CardTitle>{getRecommendations()?.title}</CardTitle>
                <CardDescription>Individualios rekomendacijos pagal jūsų VMI</CardDescription>
              </CardHeader>
              <CardContent>{getRecommendations()?.description}</CardContent>
            </Card>
          </TabsContent>

          <TabsContent value="meal-plan">
            <Card>
              <CardHeader>
                <CardTitle>{getMealPlan()?.title}</CardTitle>
                <CardDescription>Subalansuotas dienos mitybos planas</CardDescription>
              </CardHeader>
              <CardContent>
                <div className="space-y-6">
                  {getMealPlan()?.meals.map((meal, index) => (
                    <div key={index} className="border-b pb-4 last:border-0">
                      <h3 className="font-semibold text-lg mb-1">{meal.title}</h3>
                      <p>{meal.description}</p>
                    </div>
                  ))}
                </div>
              </CardContent>
            </Card>
          </TabsContent>

          <TabsContent value="workout-plan">
            <Card>
              <CardHeader>
                <CardTitle>{getWorkoutPlan()?.title}</CardTitle>
                <CardDescription>Savaitės treniruočių planas pagal jūsų tikslus</CardDescription>
              </CardHeader>
              <CardContent>
                <div className="space-y-4">
                  {getWorkoutPlan()?.days.map((day, index) => (
                    <div key={index} className="border-b pb-4 last:border-0">
                      <h3 className="font-semibold mb-1">{day.day}</h3>
                      <p>{day.workout}</p>
                    </div>
                  ))}
                </div>
              </CardContent>
            </Card>
          </TabsContent>
        </Tabs>
      )}
    </div>
  )
}

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mental Health Patient Simulator</title>
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://unpkg.com/lucide-react@0.263.1/dist/umd/lucide-react.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body>
    <div id="root"></div>
    
    <script type="text/babel">
        const { useState } = React;
        const { User, Heart, Brain, MessageCircle, CheckCircle, XCircle, RotateCcw, BookOpen } = lucideReact;

        const MentalHealthSimulator = () => {
          const [currentPatient, setCurrentPatient] = useState(null);
          const [currentStep, setCurrentStep] = useState(0);
          const [score, setScore] = useState(0);
          const [responses, setResponses] = useState([]);
          const [showFeedback, setShowFeedback] = useState(false);
          const [scenarioComplete, setScenarioComplete] = useState(false);

          const patients = [
            {
              id: 1,
              name: "Sarah Chen",
              age: 28,
              condition: "Major Depressive Disorder",
              background: "Recent job loss, divorced 6 months ago, lives alone",
              appearance: "Disheveled, poor eye contact, speaks quietly",
              chiefComplaint: "I can't sleep and I don't see the point in anything anymore",
              vitals: "BP: 118/76, HR: 72, Temp: 98.6°F",
              scenario: [
                {
                  nursePrompt: "You enter Sarah's room for initial assessment. She's sitting on the edge of the bed, looking down.",
                  patientStatement: "I don't know why I'm here. Nothing's going to help anyway.",
                  options: [
                    {
                      text: "Don't say that! Things will get better, you just need to think positively.",
                      correct: false,
                      feedback: "This response minimizes the patient's feelings and offers false reassurance. It can make patients feel unheard and guilty for not 'thinking positively.'"
                    },
                    {
                      text: "I can see you're struggling right now. Can you tell me more about what brought you here today?",
                      correct: true,
                      feedback: "Excellent! This validates her feelings while using an open-ended question to gather more information. You're showing empathy without minimizing her experience."
                    },
                    {
                      text: "You're here because you need help. Let's focus on getting you feeling better.",
                      correct: false,
                      feedback: "This response is too directive and doesn't acknowledge her emotional state. It may feel dismissive to someone experiencing depression."
                    }
                  ]
                },
                {
                  nursePrompt: "Sarah responds: 'I lost my job three weeks ago, and I can barely get out of bed. I haven't showered in days.'",
                  patientStatement: "I feel like such a failure. My ex-husband was right about me being worthless.",
                  options: [
                    {
                      text: "You're not worthless. Your ex-husband was wrong to say that to you.",
                      correct: false,
                      feedback: "While supportive, this contradicts the patient directly, which may shut down communication. Focus on exploring her feelings instead."
                    },
                    {
                      text: "It sounds like you're being very hard on yourself during an already difficult time. What does 'failure' mean to you?",
                      correct: true,
                      feedback: "Great therapeutic communication! You're reflecting her feelings and using an open question to explore her self-perception without judgment."
                    },
                    {
                      text: "Depression makes people think negative thoughts. Once we treat it, you'll feel differently.",
                      correct: false,
                      feedback: "This medicalizes her experience too quickly and dismisses her current emotional reality. Build rapport and assess thoroughly first."
                    }
                  ]
                },
                {
                  nursePrompt: "Sarah opens up more: 'I've been thinking it would be easier if I just wasn't here anymore.'",
                  patientStatement: "Sometimes I think about taking all my sleeping pills at once.",
                  options: [
                    {
                      text: "Have you thought about how you would do this? Do you have sleeping pills at home?",
                      correct: true,
                      feedback: "Correct approach! Direct assessment of suicidal ideation requires specific questions about plan and means. This is critical safety information."
                    },
                    {
                      text: "You don't really mean that. You have so much to live for.",
                      correct: false,
                      feedback: "Never minimize or contradict suicidal statements. This could prevent the patient from sharing critical safety information."
                    },
                    {
                      text: "Let's talk about something more positive. What activities did you used to enjoy?",
                      correct: false,
                      feedback: "Avoiding the topic of suicide when directly mentioned is dangerous. Always assess suicidal ideation thoroughly and immediately."
                    }
                  ]
                }
              ]
            },
            {
              id: 2,
              name: "Marcus Williams",
              age: 34,
              condition: "Bipolar Disorder - Manic Episode",
              background: "Software engineer, stopped taking lithium 2 weeks ago, reports not sleeping for 3 days",
              appearance: "Hyperverbal, rapid speech, distractible, dressed in bright mismatched clothes",
              chiefComplaint: "Family brought me in, but I feel amazing! I'm working on three breakthrough projects",
              vitals: "BP: 142/90, HR: 98, Temp: 99.1°F",
              scenario: [
                {
                  nursePrompt: "Marcus is pacing in his room, talking rapidly about his business ideas.",
                  patientStatement: "I don't need to be here! I've never felt better in my life. I'm about to revolutionize the tech industry!",
                  options: [
                    {
                      text: "That's great that you're feeling so energetic! Tell me about your projects.",
                      correct: false,
                      feedback: "This reinforces manic thinking. While it seems supportive, it doesn't address the concerning presentation or gather necessary assessment data."
                    },
                    {
                      text: "I can see you have a lot of energy right now. Your family was concerned about you - can you help me understand what's been happening?",
                      correct: true,
                      feedback: "Excellent! You acknowledge his energy without reinforcing grandiosity, and you gather collateral information while showing respect for his perspective."
                    },
                    {
                      text: "You need to calm down and sit still so we can talk properly.",
                      correct: false,
                      feedback: "This is confrontational and unrealistic. Patients in manic episodes often cannot 'calm down' on command and may become agitated by such requests."
                    }
                  ]
                },
                {
                  nursePrompt: "Marcus continues: 'I haven't slept in three days because I'm too excited about my ideas! Sleep is for people without vision.'",
                  patientStatement: "I also stopped taking that lithium stuff - it was slowing down my brilliant mind.",
                  options: [
                    {
                      text: "Not sleeping for three days isn't healthy. You need your medication to stay stable.",
                      correct: false,
                      feedback: "While factually correct, this direct confrontation may increase agitation. Use a more collaborative approach with patients experiencing mania."
                    },
                    {
                      text: "It sounds like you stopped your lithium because you felt it was affecting your thinking. What changes did you notice when you stopped?",
                      correct: true,
                      feedback: "Perfect! You're exploring his reasoning without being confrontational, which helps you understand his medication adherence issues and current symptoms."
                    },
                    {
                      text: "Your lithium levels must be dangerously low. We need to restart it immediately.",
                      correct: false,
                      feedback: "This medical focus skips the important step of understanding the patient's perspective and building therapeutic rapport."
                    }
                  ]
                }
              ]
            },
            {
              id: 3,
              name: "Emma Rodriguez",
              age: 19,
              condition: "Generalized Anxiety Disorder with Panic Attacks",
              background: "College freshman, first time away from home, recent panic attacks during exams",
              appearance: "Fidgety, rapid breathing, frequently checking phone, appears tired",
              chiefComplaint: "I keep having these episodes where I can't breathe and think I'm dying",
              vitals: "BP: 128/82, HR: 102, Temp: 98.4°F",
              scenario: [
                {
                  nursePrompt: "Emma is sitting on the edge of the chair, leg bouncing, looking around the room anxiously.",
                  patientStatement: "I'm sorry, I probably shouldn't even be here. I'm probably just overreacting to everything.",
                  options: [
                    {
                      text: "Don't apologize! If you're here, then what you're experiencing is real and important. Can you tell me about these episodes?",
                      correct: true,
                      feedback: "Excellent response! You validate her experience, normalize seeking help, and gather important assessment information with an open question."
                    },
                    {
                      text: "Anxiety is very common in college students. You're probably just stressed about school.",
                      correct: false,
                      feedback: "This minimizes her experience and makes assumptions. Always gather thorough assessment data before offering explanations."
                    },
                    {
                      text: "You seem really nervous. Try to relax and take some deep breaths.",
                      correct: false,
                      feedback: "While well-intentioned, telling an anxious person to 'relax' is often ineffective and can feel dismissive of their distress."
                    }
                  ]
                },
                {
                  nursePrompt: "Emma describes: 'It happens suddenly - my heart races, I can't catch my breath, and I feel like I'm going to die or go crazy.'",
                  patientStatement: "The last one happened during my chemistry exam. I had to run out of the classroom. It was so embarrassing.",
                  options: [
                    {
                      text: "That sounds really frightening. How long do these episodes usually last?",
                      correct: true,
                      feedback: "Great empathetic response! You validate her fear and gather important clinical information about duration, which helps with assessment."
                    },
                    {
                      text: "Panic attacks can't actually hurt you. You just need to remember that when they happen.",
                      correct: false,
                      feedback: "While factually accurate, this dismisses how terrifying panic attacks feel and doesn't provide practical coping strategies."
                    },
                    {
                      text: "Have you tried studying more? Sometimes being better prepared helps with test anxiety.",
                      correct: false,
                      feedback: "This assumes the panic is only about academic performance and misses the broader clinical picture of panic disorder."
                    }
                  ]
                }
              ]
            }
          ];

          const startScenario = (patient) => {
            setCurrentPatient(patient);
            setCurrentStep(0);
            setScore(0);
            setResponses([]);
            setShowFeedback(false);
            setScenarioComplete(false);
          };

          const selectResponse = (option) => {
            const newResponses = [...responses, {
              step: currentStep,
              selected: option.text,
              correct: option.correct,
              feedback: option.feedback
            }];
            setResponses(newResponses);
            
            if (option.correct) {
              setScore(score + 1);
            }
            
            setShowFeedback(true);
          };

          const nextStep = () => {
            if (currentStep < currentPatient.scenario.length - 1) {
              setCurrentStep(currentStep + 1);
              setShowFeedback(false);
            } else {
              setScenarioComplete(true);
            }
          };

          const resetSimulator = () => {
            setCurrentPatient(null);
            setCurrentStep(0);
            setScore(0);
            setResponses([]);
            setShowFeedback(false);
            setScenarioComplete(false);
          };

          const getScoreColor = (score, total) => {
            const percentage = (score / total) * 100;
            if (percentage >= 80) return 'text-green-600';
            if (percentage >= 60) return 'text-yellow-600';
            return 'text-red-600';
          };

          if (!currentPatient) {
            return (
              <div className="max-w-6xl mx-auto p-6 bg-gradient-to-br from-blue-50 to-green-50 min-h-screen">
                <div className="text-center mb-8">
                  <div className="flex items-center justify-center mb-4">
                    <Brain className="w-12 h-12 text-blue-600 mr-3" />
                    <h1 className="text-4xl font-bold text-gray-800">Mental Health Patient Simulator</h1>
                  </div>
                  <p className="text-lg text-gray-600 max-w-3xl mx-auto">
                    Practice therapeutic communication and assessment skills with realistic patient scenarios. 
                    Each interaction provides immediate feedback to help you develop confident, empathetic nursing care.
                  </p>
                </div>

                <div className="grid md:grid-cols-3 gap-6">
                  {patients.map((patient) => (
                    <div key={patient.id} className="bg-white rounded-xl shadow-lg p-6 hover:shadow-xl transition-shadow">
                      <div className="flex items-center mb-4">
                        <User className="w-8 h-8 text-blue-500 mr-3" />
                        <div>
                          <h3 className="text-xl font-semibold text-gray-800">{patient.name}</h3>
                          <p className="text-sm text-gray-500">Age: {patient.age}</p>
                        </div>
                      </div>
                      
                      <div className="mb-4">
                        <div className="flex items-center mb-2">
                          <Heart className="w-4 h-4 text-red-500 mr-2" />
                          <span className="font-medium text-gray-700">Condition:</span>
                        </div>
                        <p className="text-sm text-gray-600 ml-6">{patient.condition}</p>
                      </div>

                      <div className="mb-4">
                        <div className="flex items-center mb-2">
                          <MessageCircle className="w-4 h-4 text-green-500 mr-2" />
                          <span className="font-medium text-gray-700">Chief Complaint:</span>
                        </div>
                        <p className="text-sm text-gray-600 ml-6 italic">"{patient.chiefComplaint}"</p>
                      </div>

                      <div className="mb-6">
                        <p className="text-xs text-gray-500 mb-2">Background: {patient.background}</p>
                        <p className="text-xs text-gray-500">Appearance: {patient.appearance}</p>
                      </div>

                      <button
                        onClick={() => startScenario(patient)}
                        className="w-full bg-blue-600 text-white py-3 px-4 rounded-lg hover:bg-blue-700 transition-colors font-medium"
                      >
                        Start Assessment
                      </button>
                    </div>
                  ))}
                </div>

                <div className="mt-12 bg-white rounded-xl shadow-lg p-6">
                  <div className="flex items-center mb-4">
                    <BookOpen className="w-6 h-6 text-purple-600 mr-3" />
                    <h2 className="text-2xl font-semibold text-gray-800">Learning Objectives</h2>
                  </div>
                  <div className="grid md:grid-cols-2 gap-6">
                    <div>
                      <h3 className="font-semibold text-gray-700 mb-2">Therapeutic Communication</h3>
                      <ul className="text-sm text-gray-600 space-y-1">
                        <li>• Practice active listening techniques</li>
                        <li>• Use open-ended questions effectively</li>
                        <li>• Validate patient emotions appropriately</li>
                        <li>• Avoid non-therapeutic responses</li>
                      </ul>
                    </div>
                    <div>
                      <h3 className="font-semibold text-gray-700 mb-2">Clinical Assessment</h3>
                      <ul className="text-sm text-gray-600 space-y-1">
                        <li>• Conduct comprehensive mental status exams</li>
                        <li>• Assess suicide risk appropriately</li>
                        <li>• Recognize signs of various mental health conditions</li>
                        <li>• Document findings accurately</li>
                      </ul>
                    </div>
                  </div>
                </div>
              </div>
            );
          }

          if (scenarioComplete) {
            const totalSteps = currentPatient.scenario.length;
            const percentage = Math.round((score / totalSteps) * 100);
            
            return (
              <div className="max-w-4xl mx-auto p-6 bg-gradient-to-br from-blue-50 to-green-50 min-h-screen">
                <div className="bg-white rounded-xl shadow-lg p-8 text-center">
                  <div className="mb-6">
                    {percentage >= 80 ? (
                      <CheckCircle className="w-16 h-16 text-green-500 mx-auto mb-4" />
                    ) : (
                      <XCircle className="w-16 h-16 text-yellow-500 mx-auto mb-4" />
                    )}
                    <h2 className="text-3xl font-bold text-gray-800 mb-2">Assessment Complete!</h2>
                    <p className="text-lg text-gray-600">Patient: {currentPatient.name}</p>
                  </div>

                  <div className="mb-8">
                    <div className={`text-4xl font-bold mb-2 ${getScoreColor(score, totalSteps)}`}>
                      {score}/{totalSteps} ({percentage}%)
                    </div>
                    <p className="text-gray-600">
                      {percentage >= 80 ? "Excellent therapeutic communication!" :
                       percentage >= 60 ? "Good progress - review feedback for improvement" :
                       "Keep practicing - focus on therapeutic techniques"}
                    </p>
                  </div>

                  <div className="text-left mb-8 max-w-2xl mx-auto">
                    <h3 className="text-xl font-semibold mb-4 text-gray-800">Your Responses Review:</h3>
                    {responses.map((response, index) => (
                      <div key={index} className={`mb-4 p-4 rounded-lg border ${response.correct ? 'bg-green-50 border-green-200' : 'bg-red-50 border-red-200'}`}>
                        <div className="flex items-start mb-2">
                          {response.correct ? (
                            <CheckCircle className="w-5 h-5 text-green-500 mr-2 mt-0.5 flex-shrink-0" />
                          ) : (
                            <XCircle className="w-5 h-5 text-red-500 mr-2 mt-0.5 flex-shrink-0" />
                          )}
                          <div>
                            <p className="font-medium text-gray-800">Step {index + 1}:</p>
                            <p className="text-sm text-gray-600 italic mb-2">"{response.selected}"</p>
                            <p className="text-sm text-gray-700">{response.feedback}</p>
                          </div>
                        </div>
                      </div>
                    ))}
                  </div>

                  <div className="flex gap-4 justify-center">
                    <button
                      onClick={resetSimulator}
                      className="flex items-center px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors"
                    >
                      <RotateCcw className="w-4 h-4 mr-2" />
                      Try Another Patient
                    </button>
                    <button
                      onClick={() => startScenario(currentPatient)}
                      className="px-6 py-3 bg-green-600 text-white rounded-lg hover:bg-green-700 transition-colors"
                    >
                      Retry This Scenario
                    </button>
                  </div>
                </div>
              </div>
            );
          }

          const currentScenario = currentPatient.scenario[currentStep];

          return (
            <div className="max-w-4xl mx-auto p-6 bg-gradient-to-br from-blue-50 to-green-50 min-h-screen">
              <div className="bg-white rounded-xl shadow-lg overflow-hidden">
                {/* Patient Header */}
                <div className="bg-blue-600 text-white p-6">
                  <div className="flex items-center justify-between">
                    <div className="flex items-center">
                      <User className="w-8 h-8 mr-3" />
                      <div>
                        <h2 className="text-2xl font-bold">{currentPatient.name}</h2>
                        <p className="text-blue-100">{currentPatient.condition} • Age: {currentPatient.age}</p>
                      </div>
                    </div>
                    <div className="text-right">
                      <p className="text-blue-100">Step {currentStep + 1} of {currentPatient.scenario.length}</p>
                      <p className="text-blue-100">Score: {score}/{currentStep + (showFeedback ? 1 : 0)}</p>
                    </div>
                  </div>
                </div>

                {/* Scenario Content */}
                <div className="p-6">
                  {!showFeedback ? (
                    <>
                      <div className="mb-6 p-4 bg-gray-50 rounded-lg">
                        <h3 className="font-semibold text-gray-800 mb-2">Situation:</h3>
                        <p className="text-gray-700">{currentScenario.nursePrompt}</p>
                      </div>

                      <div className="mb-6 p-4 bg-blue-50 rounded-lg border-l-4 border-blue-500">
                        <h3 className="font-semibold text-blue-800 mb-2">Patient says:</h3>
                        <p className="text-blue-700 italic">"{currentScenario.patientStatement}"</p>
                      </div>

                      <div className="mb-6">
                        <h3 className="font-semibold text-gray-800 mb-4">How do you respond?</h3>
                        <div className="space-y-3">
                          {currentScenario.options.map((option, index) => (
                            <button
                              key={index}
                              onClick={() => selectResponse(option)}
                              className="w-full text-left p-4 border border-gray-200 rounded-lg hover:bg-gray-50 hover:border-blue-300 transition-colors"
                            >
                              {option.text}
                            </button>
                          ))}
                        </div>
                      </div>
                    </>
                  ) : (
                    <div className="space-y-6">
                      <div className={`p-6 rounded-lg border-l-4 ${responses[currentStep].correct ? 'bg-green-50 border-green-500' : 'bg-red-50 border-red-500'}`}>
                        <div className="flex items-start mb-4">
                          {responses[currentStep].correct ? (
                            <CheckCircle className="w-6 h-6 text-green-600 mr-3 mt-1" />
                          ) : (
                            <XCircle className="w-6 h-6 text-red-600 mr-3 mt-1" />
                          )}
                          <div>
                            <h3 className={`font-semibold text-lg ${responses[currentStep].correct ? 'text-green-800' : 'text-red-800'}`}>
                              {responses[currentStep].correct ? 'Excellent Response!' : 'Consider This Approach:'}
                            </h3>
                            <p className="text-sm text-gray-600 italic mb-3">You selected: "{responses[currentStep].selected}"</p>
                          </div>
                        </div>
                        <p className={`${responses[currentStep].correct ? 'text-green-700' : 'text-red-700'}`}>
                          {responses[currentStep].feedback}
                        </p>
                      </div>

                      <div className="flex justify-center">
                        <button
                          onClick={nextStep}
                          className="px-8 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors font-medium"
                        >
                          {currentStep < currentPatient.scenario.length - 1 ? 'Continue Assessment' : 'Complete Assessment'}
                        </button>
                      </div>
                    </div>
                  )}
                </div>
              </div>

              {/* Patient Info Panel */}
              <div className="mt-6 bg-white rounded-xl shadow-lg p-6">
                <h3 className="font-semibold text-gray-800 mb-4">Patient Information</h3>
                <div className="grid md:grid-cols-2 gap-4 text-sm">
                  <div>
                    <p className="text-gray-600"><span className="font-medium">Background:</span> {currentPatient.background}</p>
                    <p className="text-gray-600 mt-2"><span className="font-medium">Appearance:</span> {currentPatient.appearance}</p>
                  </div>
                  <div>
                    <p className="text-gray-600"><span className="font-medium">Chief Complaint:</span> "{currentPatient.chiefComplaint}"</p>
                    <p className="text-gray-600 mt-2"><span className="font-medium">Vitals:</span> {currentPatient.vitals}</p>
                  </div>
                </div>
              </div>
            </div>
          );
        };

        ReactDOM.render(<MentalHealthSimulator />, document.getElementById('root'));
    </script>
</body>
</html>
![image](https://github.com/user-attachments/assets/f34572b5-1bc7-4bd9-a785-13339d96dabf)


import React, { useEffect, useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { differenceInDays, format, isBefore, isToday } from "date-fns";

export default function CountdownCalendar() {
  const [events, setEvents] = useState([
    {
      name: "My Birthday",
      date: new Date("2025-06-30"),
      emoji: "🎉"
    },
    {
      name: "Exam Day",
      date: new Date("2025-05-20"),
      emoji: "📚"
    }
  ]);
  const [currentDate, setCurrentDate] = useState(new Date());
  const [darkMode, setDarkMode] = useState(false);

  useEffect(() => {
    const interval = setInterval(() => {
      setCurrentDate(new Date());
    }, 1000 * 60 * 60); // Update every hour

    return () => clearInterval(interval);
  }, []);

  const getCalendarDays = () => {
    const daysInMonth = new Date(
      currentDate.getFullYear(),
      currentDate.getMonth() + 1,
      0
    ).getDate();
    return Array.from({ length: daysInMonth }, (_, i) => i + 1);
  };

  const handleAddEvent = (e) => {
    e.preventDefault();
    const name = e.target.name.value;
    const date = new Date(e.target.date.value);
    const emoji = e.target.emoji.value || "🎯";
    setEvents([...events, { name, date, emoji }]);
    e.target.reset();
  };

  return (
    <div className={`${darkMode ? "bg-gray-900 text-white" : "bg-white text-black"} min-h-screen p-4`}>      
      <div className="max-w-xl mx-auto text-center">
        <h1 className="text-3xl font-bold mb-2">Countdown Events Calendar</h1>
        <button
          className="mb-4 px-4 py-2 rounded bg-blue-500 text-white"
          onClick={() => setDarkMode(!darkMode)}
        >
          Toggle {darkMode ? "Light" : "Dark"} Mode
        </button>

        {events.map((event, index) => {
          const daysLeft = differenceInDays(event.date, currentDate);
          return (
            <p key={index} className="text-lg mb-1">
              {event.emoji} <strong>{event.name}</strong>: {daysLeft >= 0 ? `${daysLeft} days left` : "Completed!"} ({format(event.date, "MMMM d, yyyy")})
            </p>
          );
        })}

        <form onSubmit={handleAddEvent} className="my-6 flex flex-col gap-2 items-center">
          <input type="text" name="name" placeholder="Event Name" required className="p-2 rounded border" />
          <input type="date" name="date" required className="p-2 rounded border" />
          <input type="text" name="emoji" placeholder="Emoji (optional)" className="p-2 rounded border" />
          <button type="submit" className="px-4 py-2 bg-green-500 text-white rounded">Add Event</button>
        </form>

        <div className="grid grid-cols-7 gap-2">
          {getCalendarDays().map((day) => {
            const date = new Date(
              currentDate.getFullYear(),
              currentDate.getMonth(),
              day
            );
            const completed = isBefore(date, currentDate);
            const today = isToday(date);

            return (
              <Card
                key={day}
                className={`${completed ? "bg-gray-400 line-through" : darkMode ? "bg-gray-800" : "bg-white"} ${today ? "border-2 border-blue-500" : ""}`}
              >
                <CardContent className="p-2 font-semibold text-center">
                  {day}
                  <div className="text-sm">
                    {events.map((event, i) =>
                      isToday(event.date) && event.date.getDate() === day ? (
                        <div key={i}>{event.emoji}</div>
                      ) : null
                    )}
                  </div>
                </CardContent>
              </Card>
            );
          })}
        </div>
      </div>
    </div>
  );
}

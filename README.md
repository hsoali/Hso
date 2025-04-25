import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";
import { authTables } from "@convex-dev/auth/server";

export default defineSchema({
  ...authTables,
  rooms: defineTable({
    code: v.string(),
    status: v.string(),
    currentPuzzle: v.number(),
    maxPlayers: v.number(),
    minPlayers: v.optional(v.number()),
    creatorId: v.optional(v.id("users"))
  }).index("by_code", ["code"]),
  
  players: defineTable({
    roomId: v.id("rooms"),
    name: v.optional(v.string()),
    color: v.string(),
    position: v.object({
      x: v.number(),
      y: v.number()
    }),
    ready: v.boolean(),
    userId: v.optional(v.id("users"))
  }).index("by_room", ["roomId"]),

  puzzles: defineTable({
    roomId: v.id("rooms"),
    type: v.string(),
    data: v.object({
      solution: v.array(v.string()),
      hints: v.array(v.string()),
      state: v.object({
        buttons: v.optional(v.array(v.string())),
        wires: v.optional(v.array(v.object({
          from: v.string(),
          to: v.string()
        })))
      })
    }),
    solved: v.boolean()
  }).index("by_room", ["roomId"])
});
